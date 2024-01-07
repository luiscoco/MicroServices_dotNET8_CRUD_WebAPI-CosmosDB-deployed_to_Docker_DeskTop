# How to deploy  to Docker DeskTop a .NET8 WebAPI CRUD CosmosDB Microservice

## 0. Prerequisites

- Download and run Docker Desktop

- Install Visual Studio 2022 Community Edition

## 1. Create .NET8 WebAPI CRUD CosmosDB Microservice

See this repo: https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-AzureCosmosDB

## 2. Create the Dockerfile

We add Docker support in the application and automatically Visual Studio creates a Dockerfile

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/3e557814-6763-4d42-9265-60a6e772f700)

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/af268365-edd9-4d6c-8267-53fd7caba91b)

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/0c6d767d-e0f6-4cd5-a3c2-f0830f171ca2)

This is the **Dockerfile** source code

```
#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["AzureCosmosCRUDWebAPI.csproj", "."]
RUN dotnet restore "./././AzureCosmosCRUDWebAPI.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "./AzureCosmosCRUDWebAPI.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./AzureCosmosCRUDWebAPI.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "AzureCosmosCRUDWebAPI.dll"]
```

## 3. Create the Docker image

```
docker login
```

```
docker build -t azurecosmoscrudwebapi .
```

We can see the new docker image

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/898a8a67-47fc-4e49-9603-c67777d58c65)

## 4. Run the Docker container

```
docker run -d -p 80:8080 --name myapp azurecosmoscrudwebapi
```

We verify the running container docker image

```
docker ps
```

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/544b712f-13d1-4d3c-bc50-ae19085bda8d)

We also can see in Docker Desktop the running Docker image

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/4977f5a4-47db-4f2f-b959-d33342485952)

## 5. Verify the application

We send the Get all items request

http://localhost/api/family

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/5f46c76c-d7bd-4b6a-b2dd-8ea66e5d12bc)

We can also send the Get by Id item request

http://localhost/api/family/1?partitionKeyValue=pk001

![image](https://github.com/luiscoco/MicroServices_dotNET8_CRUD_WebAPI-deployed_to_Docker_DeskTop/assets/32194879/fcd9cc0f-14fd-40b4-bd88-f76746841cf4)
