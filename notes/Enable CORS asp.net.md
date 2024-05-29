```csharp

// in builder
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


// with app obj
app.UseCors("AllowAll");
```

[[cors]]
[[asp.net]]