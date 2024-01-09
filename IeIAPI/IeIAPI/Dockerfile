# Etapa de construcción
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

COPY ./IeIAPI.csproj .
RUN dotnet restore

COPY . .
RUN dotnet build -c Release -o /app/build

# Etapa de publicación
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS publish
WORKDIR /app
COPY --from=build /app/build .

# Etapa final con Selenium y ChromeDriver
FROM selenium/standalone-chrome:latest
USER root

# Instalar dependencias adicionales si es necesario
# RUN apt-get update && apt-get install -y <nombre-del-paquete>

WORKDIR /app
COPY --from=publish /app .

# Exponer los puertos
EXPOSE 5000
EXPOSE 56581

# Comando para ejecutar la aplicación al iniciar el contenedor
CMD ["dotnet", "IeIAPI.dll"]