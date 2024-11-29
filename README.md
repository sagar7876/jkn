## SerialogWithSeq/Controllers/WeatherForecastController.cs
using Microsoft.AspNetCore.Mvc;
using Serilog;

namespace SerialogWithSeq.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> _logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger)
        {
            _logger = logger;
        }

        [HttpGet(Name = "GetWeatherForecast")]
        public IEnumerable<WeatherForecast> Get()
        {
            Log.Information("Weather get info start");
            try
            {
                int zero = 0;
                int b = 5 / zero;
                return Enumerable.Range(1, 5).Select(index => new WeatherForecast
                {
                    Date = DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
                    TemperatureC = Random.Shared.Next(-20, 55),
                    Summary = Summaries[Random.Shared.Next(Summaries.Length)]
                })
                .ToArray();
            }
            catch (Exception ex)
            {
                Log.Error("Weather get info error : ", ex.Message);
                throw;
            }
        }
    }
}


## SerialogWithSeq/Program.cs
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers();
// Learn more about configuring OpenAPI at https://aka.ms/aspnet/openapi
builder.Services.AddOpenApi();

var app = builder.Build();

//Log.Logger = new LoggerConfiguration()
//    .MinimumLevel.Information()  // Adjust the minimum level as needed
//    .WriteTo.Seq("http://localhost:5341") // The URL where your Seq instance is running
//    .CreateLogger();

Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .Enrich.WithMachineName()     // Logs the machine name
    .Enrich.WithProcessId()       // Logs the process ID
    .Enrich.WithThreadId()        // Logs the thread ID
    .WriteTo.Seq("http://localhost:5341") // Your Seq server URL
    .CreateLogger();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}

app.UseAuthorization();

app.MapControllers();

app.Run();


## SerialogWithSeq/Properties/PublishProfiles/FolderProfile.pubxml
﻿<?xml version="1.0" encoding="utf-8"?>
<!--
https://go.microsoft.com/fwlink/?LinkID=208121.
-->
<Project>
  <PropertyGroup>
    <DeleteExistingFiles>false</DeleteExistingFiles>
    <ExcludeApp_Data>false</ExcludeApp_Data>
    <LaunchSiteAfterPublish>true</LaunchSiteAfterPublish>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <PublishProvider>FileSystem</PublishProvider>
    <PublishUrl>C:\Deployment</PublishUrl>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <_TargetId>Folder</_TargetId>
  </PropertyGroup>
</Project>

## SerialogWithSeq/Properties/PublishProfiles/FolderProfile.pubxml.user
﻿<?xml version="1.0" encoding="utf-8"?>
<!--
https://go.microsoft.com/fwlink/?LinkID=208121.
-->
<Project>
  <PropertyGroup>
    <_PublishTargetUrl>C:\Deployment</_PublishTargetUrl>
    <History>True|2024-11-28T16:54:17.3446812Z||;</History>
    <LastFailureDetails />
  </PropertyGroup>
</Project>

## SerialogWithSeq/SerialogWithSeq.csproj
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net9.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="9.0.0" />
    <PackageReference Include="Serilog" Version="4.1.0" />
    <PackageReference Include="Serilog.Enrichers.Environment" Version="3.0.1" />
    <PackageReference Include="Serilog.Enrichers.Process" Version="3.0.0" />
    <PackageReference Include="Serilog.Enrichers.Thread" Version="4.0.0" />
    <PackageReference Include="Serilog.Sinks.Seq" Version="8.0.0" />
  </ItemGroup>

</Project>


## SerialogWithSeq/SerialogWithSeq.csproj.user
﻿<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="Current" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <NameOfLastUsedPublishProfile>C:\Sagar\Koder AI\SeialogWithSeq\SerialogWithSeq\SerialogWithSeq\Properties\PublishProfiles\FolderProfile.pubxml</NameOfLastUsedPublishProfile>
  </PropertyGroup>
</Project>

## SerialogWithSeq/SerialogWithSeq.http
@SerialogWithSeq_HostAddress = http://localhost:5250

GET {{SerialogWithSeq_HostAddress}}/weatherforecast/
Accept: application/json

###


## SerialogWithSeq/WeatherForecast.cs
namespace SerialogWithSeq
{
    public class WeatherForecast
    {
        public DateOnly Date { get; set; }

        public int TemperatureC { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);

        public string? Summary { get; set; }
    }
}


