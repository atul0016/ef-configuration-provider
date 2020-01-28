# ef-configuration-provider
An ASP.NET Core configuration provider backed by EF core

This library allows to use a sql database table for configuration values in an ASP.NET Core project.

# Install

https://www.nuget.org/packages/EfConfigurationProvider/


```
dotnet add package EfConfigurationProvider.Core
dotnet add package EfConfigurationProvider.Api
dotnet add package EfConfigurationProvider.Ui

// add one of the following database providers
dotnet add package EfConfigurationProvider.Core.SqlServer
dotnet add package EfConfigurationProvider.Core.Sqlite
```

# Configure

in Program cs
```cs

using EfConfigurationProvider.Core;

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    return WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((context, conf) =>
        {
            // depending on the database you want to use, use one of the following configurations

            // SQL Server
            var cs = "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=config-test;Integrated Security=True;Connect Timeout=30;Encrypt=False;TrustServerCertificate=False;ApplicationIntent=ReadWrite;MultiSubnetFailover=False";
            conf.AddEFConfiguration(options => options.UseSqlServer(cs, options =>
            {
                options.MigrationsAssembly("EfConfigurationProvider.Core.SqlServer");
            }));

            // SQLite
            var cs = "Data Source=sample.sqlite";
            conf.AddEFConfiguration(options => options.UseSqlite(cs, options =>
            {
                options.MigrationsAssembly("EfConfigurationProvider.Core.Sqlite");
            }));
        })
        .UseStartup<Startup>();
}
```

in Startup.cs
```cs
using EfConfigurationProvider.Core;
using EfConfigurationProvider.Ui;


public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddEfConfiguration();
    services.AddEfConfigurationUi();
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseMvc();
    app.UseEfConfigurationUi(); // enables a ui served under  /__configuration/index.html
}
```

# Features / Roadmap

 - :white_check_mark: Simple table based edit of all key/values
 - :white_check_mark: MS SQL provider
 - :white_check_mark: Embedded frontend
 - :white_check_mark: Save history
 - :black_square_button: Allow rollbacks
 - :black_square_button: Configurable authorization
 - :black_square_button: Configurable routes (for frontend and backend)
 - :black_square_button: Postgresql, MySQL, SQLite provider
