## Create project linux cli
```shell
dotnet new webapi --use-controllers
```
==Create project in current directory==
## CORS allow `Program.cs`
```cs
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll",
        builder =>
        {
            builder.AllowAnyOrigin()
                   .AllowAnyMethod()
                   .AllowAnyHeader();
        });
});
```

## Example of DI `Program.cs`
```cs
builder.Services.AddScoped<SomeService>();
builder.Services.AddSingleton<SomeService>();
builder.Services.AddTransient<SomeService>();
```
## DTO example
```cs
using System.Text.Json.Serialization;


public class TokenDto
{
    public TokenDTO(Token hash)
    {
        Hash = hash.Hash;
    }
    public TokenDTO() { }

    [JsonPropertyName("hash")]
    public string Hash { get; set; } = string.Empty;
}
```
## DB
#### MariaDB connect  `Program.cs`
```cs
using Microsoft.EntityFrameworkCore;

builder.Services.AddDbContext<Context>(options =>
{
    string? connString = Environment.GetEnvironmentVariable("DB_CONNECTION_STRING");

    if (connString == null)
    {
        throw new Exception("DB_CONNECTION_STRING not defined");
    }

    ServerVersion serverVersion = ServerVersion.Parse(connString);
    options.UseMySql(connString, serverVersion);
});
```
#### Auto create db `Program.cs`
```cs
Context.CreateDb(app);
```
### Simple relations
```cs
using Microsoft.EntityFrameworkCore;


public class Context(DbContextOptions<DbService> options) : DbContext(options)
{
    public DbSet<User> Users => Set<User>();
    public DbSet<Token> Tokens => Set<Token>();
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Token
        modelBuilder.Entity<Token>()
            .HasOne<User>()
            .WithMany()
            .HasForeignKey("UserId");
        modelBuilder.Entity<Token>()
            .Property(x => x.Hash)
            .IsRequired()
            .HasDefaultValue("");

        // User
        modelBuilder.Entity<User>()
            .Property(x => x.Email)
            .IsRequired()
            .HasDefaultValue("");
        modelBuilder.Entity<User>()
            .Property(x => x.Name)
            .IsRequired();
        modelBuilder.Entity<User>()
            .Property(x => x.Surname)
            .IsRequired();
        modelBuilder.Entity<User>()
            .Property(x => x.Password)
            .IsRequired()
            .HasDefaultValue("");
        modelBuilder.Entity<User>()
            .HasOne<Group>()
            .WithMany()
            .HasForeignKey("ClassId")
            .IsRequired(false);
            
        // Student group
        modelBuilder.Entity<Group>()
            .Property(x => x.Name)
            .IsRequired();
    }

    static public void CreateDb(WebApplication app)
    {
        using IServiceScope scopeDb = app.Services.CreateScope();
        Context dbContext = scopeDb.ServiceProvider.GetRequiredService<Context>();
        dbContext.Database.EnsureCreated();
    }
}
```
#### `Token.cs`
```cs
using System.ComponentModel.DataAnnotations.Schema;


[Table("tokens")]
public class Token
{
    [Column("id")]
    public int Id { get; set; }

    [Column("user_id")]
    [ForeignKey("users")]
    public int UserId { get; set; }

    [Column("hash")]
    public string Hash { get; set; } = "";

    [Column("created_at")]
    public DateTime? CreatedAt { get; set; } = DateTime.UtcNow;
    
    [Column("expired_at")]
    public DateTime? ExpiredAt { get; set; } = DateTime.UtcNow.AddDays(1);
}

```
#### `User.cs`
```cs
using System.ComponentModel.DataAnnotations.Schema;


[Table("users")]
public class User
{
    [Column("id")]
    public int Id { get; set; }

    [Column("first_name")]
    public string Name { get; set; } = "";

    [Column("surname")]
    public string Surname { get; set; } = "";

    [Column("email")]
    public string Email { get; set; } = "";

    [Column("pass")]
    public string Password { get; set; } = "";

    [Column("user_role")]
    public string UserRole { get; set; } = UserRoles.Student.ToString();

    [Column("class_id")]
    public int? ClassId { get; set; }

    [Column("created_at")]
    public DateTime? CreatedAt { get; set; } = DateTime.UtcNow;

    [Column("updated_at")]
    public DateTime? UpdatedAt { get; set; } = DateTime.UtcNow;
    
    public enum UserRoles
    {
        Student,
        Teacher,
        Moderator,
        Administrator
    }
```
#### `Group.cs`
```cs
using System.ComponentModel.DataAnnotations.Schema;


[Table("groups")]
public class Group
{
    [Column("id")]
    public int Id { get; set; }

    [Column("name")]
    public string name { get; set; } = string.Empty;

    [Column("created_at")]
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
}
```

### Complex relations
```cs
using Microsoft.EntityFrameworkCore;


public class Context(DbContextOptions<Context> options) : DbContext(options)
{
    public DbSet<User> Users => Set<User>();
    public DbSet<Group> Groups => Set<Group>();
    public DbSet<Setting> Settings => Set<Setting>();
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
	    // User
        modelBuilder.Entity<User>()
            .HasOne(u => u.group)
            .WithMany(g => g.Members)
            .HasForeignKey("GroupId");

        modelBuilder.Entity<User>()
            .HasOne(u => u.setting)
            .WithMany()
            .HasForeignKey("SettingId");

		// Group
        modelBuilder.Entity<Group>()
            .HasOne(g => g.Owner)
            .WithMany()
            .HasForeignKey("OwnerId")
            .OnDelete(DeleteBehavior.Restrict);

        modelBuilder.Entity<Group>()
            .HasOne(g => g.Revisor)
            .WithMany()
            .HasForeignKey("RevisorId")
            .OnDelete(DeleteBehavior.Restrict);

        modelBuilder.Entity<Group>()
            .HasOne(g => g.Setting)
            .WithMany()
            .HasForeignKey("SettingId")
            .OnDelete(DeleteBehavior.Restrict);
        
        // Settings
        modelBuilder.Entity<Setting>()
            .HasKey(s => s.Id);
    }

    static public void CreateDb(WebApplication app)
    {
        using IServiceScope scopeDb = app.Services.CreateScope();
        Context dbContext = scopeDb.ServiceProvider.GetRequiredService<Context>();
        dbContext.Database.EnsureCreated();
    }
}
```
#### `User.cs`
```cs
using System.ComponentModel.DataAnnotations.Schema;


[Table("users")]
public class User {
    [Column("id")]
    public int Id {get; set;}

    [Column("name")]
    public string Name {get; set;} = "";

    [Column("group_id")]
    public int? GroupId { get; set; }

    [ForeignKey("GroupId")]
    public Group? group {get; set;} 

    [Column("setting_id")]
    public int? SettingId {get; set;}

    [ForeignKey("SettingId")]
    public Setting? setting {get; set;}
}
```
#### `Group.cs`
```cs
using System.ComponentModel.DataAnnotations.Schema;


[Table("groups")]
public class Group {
    [Column("id")]
    public int id {get; set;}

    [Column("name")]
    public string Name {get; set;} = "";

    [Column("owner_id")]
    public int? OwnerId { get; set; }

    [ForeignKey("OwnerId")]
    public User? Owner { get; set; }

    [Column("revisor_id")]
    public int? RevisorId { get; set; }

    [ForeignKey("RevisorId")]
    public User? Revisor { get; set; }
    public List<User> Members {get; set;} = new();

    [Column("setting_id")]
    public int? SettingId { get; set; }

    [ForeignKey("SettingId")]
    public Setting? Setting { get; set; }
    
}
```
#### `Setting.cs`
```cs
using System.ComponentModel.DataAnnotations.Schema;


[Table("settings")]
public class Setting {
    [Column("id")]
    public int Id;

    [Column("language")]
    public string Language = "en";

    [Column("name")]
    public string Name {get; set;} = "";
}
```
## Docker deploy
#### `Dockerfile`
```Dockerfile
# maintainer info
FROM debian:11 AS builder
LABEL maintainer="carrergt@gmail.com"

# install copy and install project dependences
COPY dotnet-install.sh /

RUN sh /dotnet-install.sh

FROM builder AS publish

# copy dependences
WORKDIR /app

COPY app ./

RUN dotnet publish --sc -o /build

CMD ["/build/app", "--urls", "http://0.0.0.0:5000"]
```
#### dotnet install **Debian 12**
```shell
apt update
apt install -y wget
wget https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
apt update
apt install -y dotnet-sdk-8.0
```
#### `docker-compose.yml`
```yml
services:
  backend:
    container_name: backend-test
    restart: unless-stopped
    build: .
    ports:
      - "${PORT}:5000"
    environment:
      - DB_CONNECTION_STRING=${DB_CONNECTION_STRING}
```
#### `.env`
```
DB_CONNECTION_STRING=server=10.1.1.1;database=db;port=6001;user=root;password=pass;
```
[[EF]]