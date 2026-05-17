# 17. Hosting

Hosting an ASP.NET Web API involves setting up the application to run on a server. There are several ways to host your Web API depending on your needs.

## 1. IIS (Internet Information Services)

- IIS is the most common web server for hosting ASP.NET Web APIs. It is a powerful, full-featured web server and integrates well with .NET applications.

Steps to host with IIS:

- Publish your Web API application from Visual Studio using Publish to a folder.
- Create a new IIS site, configure it to point to the folder where the Web API is published, and configure the necessary bindings.
- Make sure to install the ASP.NET feature in IIS if not already installed.
- Configure the necessary permissions for the application pool identity to access the required resources (e.g., database).

## 2. Self-Hosting (Using OWIN/Katana)

- If you want to host your Web API outside of IIS, you can use OWIN (Open Web Interface for .NET) to self-host your API. This is useful for scenarios where you want to run the API on a standalone server or in environments without IIS.

Steps for self-hosting:

- Install the necessary NuGet packages for OWIN hosting (`Microsoft.Owin.Hosting` and `Microsoft.Owin.StaticFiles`).
- Set up a `Startup` class to configure the Web API in the OWIN pipeline.

## 3. Azure Hosting

- Hosting ASP.NET Web API on Microsoft Azure can be done using Azure App Services, Azure Functions, or by hosting it on Azure Virtual Machines.
- Azure App Services provides a fully managed platform for hosting web apps and APIs.
- Azure Functions is a serverless option that is great for hosting APIs in an event-driven manner.

## 4. Docker Hosting

- You can also containerize your ASP.NET Web API using Docker and deploy it to any platform that supports Docker containers, such as Azure, AWS, or on-premises servers.
- Create a `Dockerfile` for your Web API project and then use Docker commands to build and run the container.

Example Dockerfile for ASP.NET Core 5.0:

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /src
COPY ["WebApiProject/WebApiProject.csproj", "WebApiProject/"]
RUN dotnet restore "WebApiProject/WebApiProject.csproj"
COPY . .
WORKDIR "/src/WebApiProject"
RUN dotnet build "WebApiProject.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "WebApiProject.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "WebApiProject.dll"]
```

Example Dockerfile for ASP.NET Core 8.0:

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 8080
EXPOSE 8081
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["nuget.config", "."]
COPY ["Server/Concerto.Logistics.Timeline.Api/Concerto.Logistics.Timeline.Api.csproj", "Server/Concerto.Logistics.Timeline.Api/"]
COPY ["Server/Concerto.Logistics.Application.Services/Concerto.Logistics.Application.Services.csproj", "Server/Concerto.Logistics.Application.Services/"]
COPY ["Server/Concerto.Clients/Concerto.Clients.csproj", "Server/Concerto.Clients/"]
COPY ["Server/Syltram.Infrastructure.Domain.Model/Syltram.Infrastructure.Domain.Model.csproj", "Server/Syltram.Infrastructure.Domain.Model/"]
COPY ["Server/Syltram.Infrastructure.Shared.ValueObjects/Syltram.Infrastructure.Shared.ValueObjects.csproj", "Server/Syltram.Infrastructure.Shared.ValueObjects/"]
COPY ["Server/Syltram.Infrastructure/Syltram.Infrastructure.csproj", "Server/Syltram.Infrastructure/"]
COPY ["Server/Syltram.Infrastructure.ErrorManager/Syltram.Infrastructure.ErrorManager.csproj", "Server/Syltram.Infrastructure.ErrorManager/"]
COPY ["Server/Syltram.Infrastructure.Security/Syltram.Infrastructure.Security.csproj", "Server/Syltram.Infrastructure.Security/"]
COPY ["Server/Syltram.MoveIt.Model/Syltram.MoveIt.Model.csproj", "Server/Syltram.MoveIt.Model/"]
COPY ["Server/Concerto.Logistics.Data.Query.Interfaces/Concerto.Logistics.Data.Query.Interfaces.csproj", "Server/Concerto.Logistics.Data.Query.Interfaces/"]
COPY ["Server/Concerto.Logistics.Enums/Concerto.Logistics.Enums.csproj", "Server/Concerto.Logistics.Enums/"]
COPY ["Server/Concerto.Logistics.Infrastructure.Validation/Concerto.Logistics.Infrastructure.Validation.csproj", "Server/Concerto.Logistics.Infrastructure.Validation/"]
COPY ["Server/Syltram.Integration.Internal/Syltram.Integration.Internal.csproj", "Server/Syltram.Integration.Internal/"]
COPY ["Server/Concerto.Logistics.Integration/Concerto.Logistics.Integration.csproj", "Server/Concerto.Logistics.Integration/"]
COPY ["Server/Concerto.Logistics.Infrastructure.Messaging/Concerto.Logistics.Infrastructure.Messaging.csproj", "Server/Concerto.Logistics.Infrastructure.Messaging/"]
COPY ["Server/Syltram.Infrastructure.MessagingOnServiceBus/Syltram.Infrastructure.MessagingOnServiceBus.csproj", "Server/Syltram.Infrastructure.MessagingOnServiceBus/"]
COPY ["Server/Syltram.MoveIt.Integration/Syltram.MoveIt.Integration.csproj", "Server/Syltram.MoveIt.Integration/"]
COPY ["Server/Syltram.Integration.Resolvers/Syltram.Integration.Resolvers.csproj", "Server/Syltram.Integration.Resolvers/"]
COPY ["Server/Syltram.Integration.Internal.Clients/Syltram.Integration.Internal.Clients.csproj", "Server/Syltram.Integration.Internal.Clients/"]
COPY ["Server/Concerto.Logistics.Data.Context/Concerto.Logistics.Data.Context.csproj", "Server/Concerto.Logistics.Data.Context/"]
COPY ["Server/Concerto.Logistics.Domain/Concerto.Logistics.Domain.csproj", "Server/Concerto.Logistics.Domain/"]
COPY ["Server/Concerto.Logistics.Infrastructure.Domain/Concerto.Logistics.Infrastructure.Domain.csproj", "Server/Concerto.Logistics.Infrastructure.Domain/"]
COPY ["Server/Syltram.MoveIt.OrmConfig.EfCore/Syltram.MoveIt.OrmConfig.EfCore.csproj", "Server/Syltram.MoveIt.OrmConfig.EfCore/"]
ARG FEED_ACCESSTOKEN
RUN curl -L https://raw.githubusercontent.com/Microsoft/artifacts-credprovider/master/helpers/installcredprovider.sh  | sh

ENV VSS_NUGET_EXTERNAL_FEED_ENDPOINTS="{\"endpointCredentials\": [{\"endpoint\":\"https://pkgs.dev.azure.com/am-scaleup/_packaging/ScaleUp.GlobalPackageFeed/nuget/v3/index.json\", \"username\":\"docker\", \"password\":\${FEED_ACCESSTOKEN}\"}]}"

RUN dotnet restore "Server/Concerto.Logistics.Timeline.Api/Concerto.Logistics.Timeline.Api.csproj"
COPY . .
WORKDIR "/src/Server/Concerto.Logistics.Timeline.Api"
RUN dotnet build "Concerto.Logistics.Timeline.Api.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Concerto.Logistics.Timeline.Api.csproj" -c $BUILD_CONFIGURATION -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Concerto.Logistics.Timeline.Api.dll"]
```
