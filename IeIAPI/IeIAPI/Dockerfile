# Etapa de construcción
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

COPY ./IeIAPI.csproj .
RUN dotnet restore

COPY . .
RUN dotnet build -c Release -o /app/build

# Etapa intermedia para instalar dependencias
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS install-dependencies
WORKDIR /app

RUN apt-get update && apt-get install -y \
    wget \
    unzip \
    libgdiplus \
    libxss1 \
    libappindicator3-1 \
    libasound2 \
    libatk1.0-0 \
    libatk-bridge2.0-0 \
    libnss3 \
    libgdk-pixbuf2.0-0 \
    libgtk-3-0 \
    libx11-xcb1 \
    libxcb-dri3-0 \
    fonts-liberation \
    libgbm1 \
    libpango-1.0-0 \
    libpangocairo-1.0-0 \
    libxcursor1 \
    libxdamage1 \
    libxi6 \
    libxcomposite1 \
    libxrandr2 \
    libxss1 \
    libxtst6 \
    xdg-utils \
    libfreetype6 \
    libharfbuzz-icu0 \
    libfontconfig1 \
    libc6 \
    libgtk-3-0 \
    xvfb \
    packagekit-gtk3-module && \ 
    rm -rf /var/lib/apt/lists/*

# Instalar librerías adicionales necesarias para Firefox en modo sin cabeza
RUN apt-get update && apt-get install -y libgtk-3-0 libdbus-glib-1-2 libxt6

# Descargar e instalar Firefox y GeckoDriver
RUN apt-get update \
    && apt-get install -y firefox-esr \
    && wget -O /tmp/geckodriver.tar.gz https://github.com/mozilla/geckodriver/releases/download/v0.30.0/geckodriver-v0.30.0-linux64.tar.gz \
    && tar -zxf /tmp/geckodriver.tar.gz -C /usr/local/bin \
    && rm /tmp/geckodriver.tar.gz \
    && chmod +x /usr/local/bin/geckodriver

# Configurar el entorno sin cabeza
ENV DISPLAY=:99
ENV PATH="${PATH}:/usr/local/bin"

# Etapa de publicación
FROM build AS publish
RUN dotnet publish -c Release -o /app/publish /p:UseAppHost=false

# Etapa final
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS final
WORKDIR /app

# Copiar los archivos publicados desde la fase de publicación
COPY --from=publish /app/publish .
# Actualizar configuración de enlace dinámico
RUN ldconfig
# Exponer los puertos
EXPOSE 5000
EXPOSE 56581

# Comando para ejecutar la aplicación al iniciar el contenedor
CMD ["dotnet", "IeIAPI.dll"]