FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["src/Buckwheat.API/Buckwheat.API.csproj", "src/Buckwheat.API/"]
COPY ["src/Buckwheat.Business/Buckwheat.Business.csproj", "src/Buckwheat.Business/"]
COPY ["src/Buckwheat.Data/Buckwheat.Data.csproj", "src/Buckwheat.Data/"]
COPY ["src/Buckwheat.Domain/Buckwheat.Domain.csproj", "src/Buckwheat.Domain/"]
COPY ["src/Buckwheat.Infrastructure/Buckwheat.Infrastructure.csproj", "src/Buckwheat.Infrastructure/"]
RUN dotnet restore "src/Buckwheat.API/Buckwheat.API.csproj"
COPY . .
WORKDIR "/src/src/Buckwheat.API"
RUN dotnet build "Buckwheat.API.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "Buckwheat.API.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Buckwheat.API.dll"]
