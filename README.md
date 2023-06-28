1.dotnet new webapi -n BankAccountService

2.Change into the newly created project directory:
cd BankAccountService
3. cd BankAccountService
Set up Entity Framework and database connection:

Install the Entity Framework Core packages by running the following command:
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools

Configure your database connection in the appsettings.json file. Replace YOUR_CONNECTION_STRING with your SQL Server connection string:
json

"ConnectionStrings": {
  "DefaultConnection": "YOUR_CONNECTION_STRING"
}

4.Create a new folder named Models to store your entity classes.
Create the BankAccount model:

Inside the Models folder, create a new class file named BankAccount.cs and define the following properties:using System;

namespace BankAccountService.Models
{
    public class BankAccount
    {
        public int AccountNumber { get; set; }
        public string AccountType { get; set; }
        public string Name { get; set; }
        public string Status { get; set; }
        public decimal AvailableBalance { get; set; }
    }
}
5.Create the DbContext:

Create a new folder named Data in the project root directory.
Inside the Data folder, create a new class file named BankAccountContext.cs and define the DbContext:

using Microsoft.EntityFrameworkCore;
using BankAccountService.Models;

namespace BankAccountService.Data
{
    public class BankAccountContext : DbContext
    {
        public BankAccountContext(DbContextOptions<BankAccountContext> options)
            : base(options)
        {
        }

        public DbSet<BankAccount> BankAccounts { get; set; }
    }
}


6.Configure DbContext and services in the Startup.cs file:

Open the Startup.cs file.
In the ConfigureServices method, add the following code to configure the DbContext and enable CORS:

using BankAccountService.Data;
using Microsoft.EntityFrameworkCore;

public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BankAccountContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddControllers();

    services.AddCors(options =>
    {
        options.AddPolicy("AllowAllOrigins",
            builder =>
            {
                builder.AllowAnyOrigin()
                    .AllowAnyHeader()
                    .AllowAnyMethod();
            });
    });
}

In the Configure method, add the following code to enable CORS and configure routing:

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // ...
    app.UseRouting();

    app.UseCors("AllowAllOrigins");

    // ...
}
