# Repository guidance

## Project overview

This repository contains a .NET 8 ASP.NET Core Web API organized as a solution
of five projects:

- `Portfolio_API` hosts the API, controllers, application configuration, and
  startup.
- `Portfolio_API.Services` contains business logic, including portfolio and
  Notion services.
- `Portfolio_API.DataAccess` contains the Entity Framework Core context,
  repositories, and database migrations.
- `Portfolio_API.DataTypes` contains shared domain models, DTOs, interfaces,
  and options.
- `Portfolio_API.Mapper` contains model/DTO mapping implementations.

Keep dependencies flowing through these existing layers; controllers should
not take on persistence or business logic that belongs in the corresponding
projects.

## Build and run

Run commands from the repository root:

```sh
dotnet restore Portfolio_API.sln
dotnet build Portfolio_API.sln --configuration Release --no-restore
dotnet run --project Portfolio_API/Portfolio_API.csproj
```

There is currently no automated test project. The GitHub Actions CI workflow
restores and builds the solution; it does not run tests.

Docker files are under `docker/`. Compose and Docker builds use the repository
root as their build context:

```sh
docker compose --project-directory . -f docker/compose.yaml up --build
docker build -f docker/Dockerfile -t myapp .
```

## Change guidance

- Target .NET 8 and preserve the projects' nullable reference type and implicit
  using settings.
- Keep HTTP handling in `Portfolio_API/Controllers`, business logic in
  `Portfolio_API.Services`, EF Core persistence in `Portfolio_API.DataAccess`,
  shared types in `Portfolio_API.DataTypes`, and mapping in
  `Portfolio_API.Mapper`.
- Do not commit credentials or local environment files. Database and external
  service operations may require local configuration from `.env.example`.
- When changing build or deployment behavior, keep `.github/workflows/ci.yaml`
  and `.github/workflows/deploy.yaml` aligned with the solution.
