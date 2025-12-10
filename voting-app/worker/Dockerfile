# worker/Dockerfile
# Build stage
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY worker/ ./
RUN dotnet restore
RUN dotnet publish -c Release -o /app/publish

# Runtime
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app/publish ./

# Default envs (overridable)
ENV POSTGRES_HOST=db \
    POSTGRES_PORT=5432 \
    REDIS_HOST=redis \
    REDIS_PORT=6379

# Modification de l'ENTRYPOINT :
# Le conteneur exécute directement l'application Worker.dll.
# La logique d'attente (boucles while(true)) est maintenant gérée dans Program.cs.
ENTRYPOINT ["dotnet", "Worker.dll"]