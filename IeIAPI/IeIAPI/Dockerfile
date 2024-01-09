# Etapa de construcción
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src

COPY ./IeIAPI.csproj .
RUN dotnet restore

COPY . .
RUN dotnet build -c Release -o /app/build

# Use ADD to copy CSV and XML files
COPY ./CV.csv .
COPY ./CAT.xml .

# Etapa intermedia para instalar dependencias
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS install-dependencies
WORKDIR /app


RUN apt-get update && apt-get install -y \
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
    && rm -rf /var/lib/apt/lists/*

# Download and install Chrome
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
    && sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list' \
    && apt-get update && apt-get install -y google-chrome-stable
RUN Install-Package Selenium.WebDriver
# Set Chrome as the default browser for Selenium
ENV ASPNETCORE_DEFAULT_BROWSER=chrome



# Etapa de publicación
FROM build AS publish
RUN dotnet publish -c Release -o /app/publish /p:UseAppHost=false

# Etapa final
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS final
WORKDIR /app

# Copiar los archivos publicados desde la fase de publicación
COPY --from=publish /app/publish .

# Copiar los archivos de la etapa de instalación
COPY --from=install-dependencies /usr/bin/google-chrome-stable /usr/bin/

# Definir el punto de entrada para la aplicación
EXPOSE 5000
EXPOSE 56581
CMD ["dotnet", "IeIAPI.dll"]
