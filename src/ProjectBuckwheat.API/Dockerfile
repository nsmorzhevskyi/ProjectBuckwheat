FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/ProjectBuckwheat.API/ProjectBuckwheat.API.csproj", "src/ProjectBuckwheat.API/"]
COPY ["src/ProjectBuckwheat.Business/ProjectBuckwheat.Business.csproj", "src/ProjectBuckwheat.Business/"]
COPY ["src/ProjectBuckwheat.Data/ProjectBuckwheat.Data.csproj", "src/ProjectBuckwheat.Data/"]
COPY ["src/ProjectBuckwheat.Domain/ProjectBuckwheat.Domain.csproj", "src/ProjectBuckwheat.Domain/"]
COPY ["src/ProjectBuckwheat.Infrastructure/ProjectBuckwheat.Infrastructure.csproj", "src/ProjectBuckwheat.Infrastructure/"]

RUN dotnet restore "src/ProjectBuckwheat.API/ProjectBuckwheat.API.csproj"
COPY . .
WORKDIR "/src/src/ProjectBuckwheat.API"
RUN dotnet build "ProjectBuckwheat.API.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "ProjectBuckwheat.API.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ProjectBuckwheat.API.dll"]