# 🔄 Migracija MySQL → PostgreSQL

## Pregled
Vaša aplikacija je konfigurirana da koristi:
- **Development**: MySQL (localhost:3306)
- **Production**: PostgreSQL (Render.com)

Kod automatski bira bazu ovisno o `ASPNETCORE_ENVIRONMENT` varijabli.

---

## 📝 Opcija 1: Lokalno testiranje sa PostgreSQL

Ako želiš testirati PostgreSQL lokalno prije deployanja:

### 1. Instaliraj PostgreSQL
- Windows: Preuzmi sa [postgresql.org](https://www.postgresql.org/download/windows/)
- Tijekom instalacije, memorisira password za `postgres` usera

### 2. Kreiraj bazu
```sql
CREATE DATABASE real_estate;
```

### 3. Update appsettings.json (Production)
Kreiraj `appsettings.Production.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "MainDB": "Host=localhost;Port=5432;Username=postgres;Password=YOUR_PASSWORD;Database=real_estate"
  },
  "AllowedHosts": "*",
  "JWTSettings": {
    "securityKey": "7T-d1gfnZ_RqrWVHQYOo8ESZXDwwvPQ8ymWNGqFEm0c",
    "validIssuer": "https://localhost:5000",
    "validAudience": "https://localhost:7000",
    "expiryInMinutes": 10
  }
}
```

### 4. Kreiraj migrations za PostgreSQL
```powershell
cd api\MyProperty.API

# Ukloniti stare migracije (MySQL specifične)
Remove-Item Persistence\Migrations -Recurse -Force

# Kreiraj novu migraciju za PostgreSQL
dotnet ef migrations add InitialCreate -p Persistence -s MyProperty.API

# Primijeni migraciju
dotnet ef database update -p Persistence -s MyProperty.API
```

### 5. Pokrenni aplikaciju
```powershell
# Production environment sa PostgreSQL
$env:ASPNETCORE_ENVIRONMENT = "Production"
dotnet run --project MyProperty.API
```

---

## 📝 Opcija 2: Koristiti MySQL lokalno, PostgreSQL na Renderu

Preporučeno za brzi start - **bez lokalnih promjena**:

1. Drži `appsettings.json` sa MySQL connection stringom
2. Zadrži MySQL na lokalnoj mašini
3. Render će automatski koristiti PostgreSQL
4. Kod automatski prebacuje bazu ovisno o environmentu

---

## 🚀 Render Deploy sa automatskom migracijom

Trebat ćeš postavi `onRender` hook koji će izvršiti migraciju:

### 1. Kreiraj `run.sh` u root direktorija API-ja

```bash
#!/bin/bash
set -e

echo "Running database migrations..."
cd api/MyProperty.API

# Restore dependencies
dotnet restore

# Create migrations if they don't exist
dotnet ef migrations add InitialMigration -p Persistence -s MyProperty.API || true

# Apply migrations
dotnet ef database update -p Persistence -s MyProperty.API

echo "Migrations complete. Starting application..."

# Start app
cd MyProperty.API
dotnet MyProperty.API.dll
```

### 2. Update Render.yaml

```yaml
services:
  - type: web
    name: myproperty-api
    env: dotnet
    buildCommand: cd api/MyProperty.API && dotnet publish -c Release
    startCommand: bash api/MyProperty.API/run.sh
    # ... ostatak konfiguracije
```

---

## 🔍 Validacija: Koji se DB koristi?

### U kodu:
```csharp
var environment = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");
var isProduction = environment == "Production";

if (isProduction)
    // PostgreSQL
else
    // MySQL
```

### Na Renderu:
- Postavi `ASPNETCORE_ENVIRONMENT = Production`
- Automatski će koristiti PostgreSQL

---

## ⚙️ Entity Framework Core komande

```powershell
# Generira noviju migraciju
dotnet ef migrations add MigrationName -p Persistence -s MyProperty.API

# Primijeni sve pending migracije
dotnet ef database update -p Persistence -s MyProperty.API

# Vrati zadnju migraciju
dotnet ef migrations remove -p Persistence -s MyProperty.API

# Vrati do specifične migracije
dotnet ef database update MigrationName -p Persistence -s MyProperty.API

# Generiraj SQL umjesto primjene
dotnet ef migrations script -p Persistence -s MyProperty.API -o migration.sql
```

---

## 🎯 Preporučeni pristup

1. **Lokalno**: Nastavi sa MySQL (bez promjena)
2. **Deploy**: Render će koristiti PostgreSQL
3. **Kod**: Automatski se prilagođava ovisno o environmentu
4. **Migracije**: EF Core sam čini sve

**Rezultat**: Bez dodatnog posla, sve radi! ✅

---

## 🆘 Ako nešto krene po zlu

```powershell
# Vidi sve migracije
dotnet ef migrations list -p Persistence -s MyProperty.API

# Generiraj novi DbContext (ako je korumpiran)
dotnet ef dbcontext scaffold ... # ako trebalo

# Vidi current database state
dotnet ef migrations script -p Persistence -s MyProperty.API
```

