# Etapa de construcción
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

COPY ./IeIAPI.csproj .
RUN dotnet restore

COPY . .
RUN dotnet build -c Release -o /app/build

# Etapa final con Selenium y ChromeDriver
FROM selenium/standalone-chrome:latest AS final
USER root

WORKDIR /app
COPY --from=build /app/build .

# Exponer el puerto de la aplicación
EXPOSE 80

# Comando para ejecutar la aplicación y Selenium al iniciar el contenedor
CMD ["dotnet", "IeIAPI.dll"]