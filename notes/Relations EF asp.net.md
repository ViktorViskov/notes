Example of creating relations in asp.net

#### User
```csharp
﻿namespace Models;
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
    [Column("created_at")]
    public DateTime? CreatedAt { get; set; }
    [Column("updated_at")]
    public DateTime? UpdatedAt { get; set; }
    public enum UserRoles
    {
        Student,
        Teacher,
        Moderator,
        Administrator
    }
}
```

#### Token
```csharp
﻿namespace Models;
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

#### Db context
```csharp
﻿namespace Services;
public class DbService(DbContextOptions<DbService> options) : DbContext(options)
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
    }
    
    static public void AutoCreating(WebApplication app)
    {
        using IServiceScope scopeDb = app.Services.CreateScope();
        DbService dbContext = scopeDb.ServiceProvider.GetRequiredService<DbService>();

        bool wasCreated = dbContext.Database.EnsureCreated();
        if (wasCreated)
        {
            // Here must be data to fill up after creating db
        }
    }
}
```

#### Program.cs
```csharp
// MySQL | MariaDB example
builder.Services.AddDbContext<DbService>(options =>
{
	// from conf file
    string? connString = builder.Configuration.GetConnectionString("CONN_STR");
	// from global env
    connString = Environment.GetEnvironmentVariable("CONN_STR");

    ServerVersion serverVersion = ServerVersion.Parse(connString);
    options.UseMySql(connString, serverVersion);
});
```

[[ef]]
[[asp.net]]
[[relations]]
[[orm]]
