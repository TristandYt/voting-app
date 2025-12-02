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

# Add wait-for script (netcat is available in runtime)
COPY scripts/wait-for.sh /usr/local/bin/wait-for.sh
RUN chmod +x /usr/local/bin/wait-for.sh

# Default envs (overridable)
ENV POSTGRES_HOST=db \
    POSTGRES_PORT=5432 \
    REDIS_HOST=redis \
    REDIS_PORT=6379

# If the compiled dll has another name, replace Worker.dll by the real name from your project
ENTRYPOINT ["sh", "-c", "wait-for.sh ${POSTGRES_HOST}:${POSTGRES_PORT} -- wait-for.sh ${REDIS_HOST}:${REDIS_PORT} -- dotnet Worker.dll"]
