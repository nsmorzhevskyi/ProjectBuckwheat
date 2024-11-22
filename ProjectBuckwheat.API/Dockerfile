# Base image for running the application
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
# Setting the user to the application user
USER $APP_UID
# Setting the working directory to /app
WORKDIR /app
# Exposing ports for the application
EXPOSE 8080
EXPOSE 8081

# SDK image for building the application
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
ARG BUILD_CONFIGURATION=Release
# Setting the working directory to /src
WORKDIR /src

# Copying the project file
COPY ["ProjectBuckwheat.API.csproj", "./"]
# Log current state and contents
RUN echo "Copied ProjectBuckwheat.API.csproj:" && ls -l

# Restoring dependencies
RUN echo "Restoring dependencies for ProjectBuckwheat.API.csproj..." && \
    dotnet restore "ProjectBuckwheat.API/ProjectBuckwheat.API.csproj" && \
    echo "Dependencies restored successfully."

# Copying the remaining files
COPY . .
RUN echo "Files copied to /src:" && ls -l

# Setting the working directory for building the project
WORKDIR "/src/ProjectBuckwheat.API"
RUN echo "Current working directory: $(pwd)" && \
    echo "Building the project in $BUILD_CONFIGURATION mode..." && \
    dotnet build "ProjectBuckwheat.API.csproj" -c $BUILD_CONFIGURATION -o /app/build && \
    echo "Build completed. Listing build directory contents:" && ls -l /app/build

# Creating the publish image
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
# Publishing the application
RUN echo "Publishing the application to /app/publish..." && \
    dotnet publish "ProjectBuckwheat.API.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false && \
    echo "Publish completed. Listing publish directory contents:" && ls -l /app/publish

# Final runtime image
FROM base AS final
# Setting the working directory
WORKDIR /app
# Copying published files to the final image
COPY --from=publish /app/publish .
# Logging the final directory structure
RUN echo "Final runtime image setup. Listing /app contents:" && ls -l
# Setting the entry point for the application
ENTRYPOINT ["dotnet", "ProjectBuckwheat.API.dll"]
