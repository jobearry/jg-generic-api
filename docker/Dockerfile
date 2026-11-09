# syntax=docker/dockerfile:1

FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build

COPY . /source

WORKDIR /source/Portfolio_API

# This is the architecture you’re building for, which is passed in by the builder.
# Placing it here allows the previous steps to be cached across architectures.
ARG TARGETARCH

# Build the application.
# Leverage a cache mount to /root/.nuget/packages so that subsequent builds don't have to re-download packages.
# If TARGETARCH is "amd64", replace it with "x64" - "x64" is .NET's canonical name for this and "amd64" doesn't
#   work in .NET 6.0.
RUN --mount=type=cache,id=nuget,target=/root/.nuget/packages \
    dotnet publish -a ${TARGETARCH/amd64/x64} --use-current-runtime --self-contained false -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS final
WORKDIR /app

# Install ICU globalization libraries
RUN apk add --no-cache icu-libs

# Ensure .NET uses ICU instead of invariant mode
ENV DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=false

# Copy everything needed to run the app from the "build" stage.
COPY --from=build /app .

# Run as non-privileged user
USER $APP_UID

ENTRYPOINT ["dotnet", "Portfolio_API.dll"]

