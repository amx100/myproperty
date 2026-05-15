# 🚀 MyProperty - Quick Start Guide

## Što je izrađeno?

Vaš projekat je sada **spreman za besplatno hostovanje** na **Render.com**! 

### ✅ Sve izmjene urađene:

#### API (`api/MyProperty.API/`)
- ✅ Konfiguracija za PostgreSQL (Production) + MySQL (Development)
- ✅ JWT settings ažurirani za production domene
- ✅ Environment-specific connection stringi
- ✅ Dockerfile kreiran za containerizaciju
- ✅ .dockerignore kreiran

#### UI (`ui/MyProperty.UI/`)
- ✅ Dinamički API URL (lokalno vs. production)
- ✅ Automatska detekcija okruženja

#### Deployment
- ✅ `render.yaml` - kompletan deployment manifest
- ✅ `DEPLOYMENT_GUIDE.md` - detaljne upute
- ✅ `DATABASE_MIGRATION.md` - migracija baze podataka
- ✅ `.gitignore` - za GitHub

---

## 🎯 Sljedeći koraci

### Korak 1: Pripremi GitHub
```powershell
cd d:\Projekti\myproperty

# Inicijalizuj Git ako već nije
git init

# Dodaj sve fajlove
git add .

# Kreiraj prvi commit
git commit -m "Initial commit - ready for deployment"

# Kreiraj main granu
git branch -M main

# Dodaj remote (zamijeni sa tvojim korisničkim imenom)
git remote add origin https://github.com/YOUR_USERNAME/myproperty.git

# Push na GitHub
git push -u origin main
```

### Korak 2: Kreiraj Render.com konto
1. Idi na https://render.com
2. Klikni **Sign up** → **Continue with GitHub**
3. Autorizuj pristup
4. Potvrdi email

### Korak 3: Deploy na Renderu

#### A. Deploy PostgreSQL baze
1. Kreiraj novi dashboard
2. **New** → **PostgreSQL**
3. Popuni:
   - **Name**: `myproperty-db`
   - **Region**: `Frankfurt`
   - **PostgreSQL Version**: `16`
4. Klikni **Create Database**
5. **SPREMI**: Internal Database URL (trebat će za API)

#### B. Deploy API-ja
1. **New** → **Web Service**
2. Odaberi `myproperty` GitHub repo
3. Popuni:
   - **Name**: `myproperty-api`
   - **Build Command**: `cd api/MyProperty.API && dotnet publish -c Release`
   - **Start Command**: `cd api/MyProperty.API/MyProperty.API && dotnet MyProperty.API.dll`
4. **Environment Variables** → Dodaj:

```
ASPNETCORE_ENVIRONMENT = Production
ASPNETCORE_URLS = http://+:10000
ConnectionStrings__MainDB = [Internal Database URL iz PostgreSQL]
JWTSettings__securityKey = 7T-d1gfnZ_RqrWVHQYOo8ESZXDwwvPQ8ymWNGqFEm0c
JWTSettings__validIssuer = https://myproperty-api.onrender.com
JWTSettings__validAudience = https://myproperty-ui.onrender.com
```

5. Klikni **Deploy**

#### C. Deploy UI-ja
1. **New** → **Static Site**
2. Odaberi isti GitHub repo
3. Popuni:
   - **Name**: `myproperty-ui`
   - **Build Command**: `cd ui/MyProperty.UI/MyProperty.UI && dotnet publish -c Release -o dist`
   - **Publish Directory**: `ui/MyProperty.UI/MyProperty.UI/bin/Release/net8.0/publish/wwwroot`
4. Klikni **Deploy**

---

## 📊 Šta se desilo u kodu?

### Prije
```csharp
// Hardkodirano - samo localhost
var connectionString = "Server=localhost;Port=3306;...";
```

### Sada
```csharp
// Automatski izbor ovisno o okruženju
if (environment == "Production")
    // PostgreSQL (Render)
else
    // MySQL (Local)
```

---

## 🔧 Lokalni razvoj (bez promjena)

```powershell
# Što trebalo prije - ISTA KOMANDA
cd api\MyProperty.API
dotnet run

# U drugoj terminal:
cd ui\MyProperty.UI\MyProperty.UI
dotnet run
```

**Lokalno koristi MySQL** - nema promjena! ✅

---

## 🆘 Troubleshooting

### ❌ Build fails na Renderu
**Rješenje**: Provjeri da li su svi `.csproj` fajlovi dostupni
```powershell
cd api/MyProperty.API
dotnet restore
dotnet publish -c Release
```

### ❌ CORS greška na UI
**Rješenje**: Provjeri Environment variables u API deployment
- `JWTSettings__validAudience` mora biti tačna URL UI-ja

### ❌ Database connection greška
**Rješenje**: Kopiraj `Internal Database URL` iz PostgreSQL i spremi kao:
```
ConnectionStrings__MainDB = postgresql://user:password@host:port/database
```

---

## 📚 Dostupna dokumentacija

| Fajl | Opis |
|------|------|
| `DEPLOYMENT_GUIDE.md` | 📘 Detaljne upute za deployment |
| `DATABASE_MIGRATION.md` | 🔄 Migracija MySQL → PostgreSQL |
| `render.yaml` | ⚙️ Render deployment konfiguracija |
| `Dockerfile` | 🐳 Docker image za API |

---

## ✨ Što je posebno?

✅ **Besplatno** - Render.com free tier  
✅ **Auto-scaling** - Ako primjena poraste  
✅ **SSL/TLS** - Automatski certifikat  
✅ **PostgreSQL** - Besplatna baza do 90 dana  
✅ **No cold starts** - Ako plaćaš premium  

---

## 💡 Savjet: Custom domene

Nakon što deploy radi, možeš dodati custom domene:

1. Kupi domen na GoDaddy, Namecheap, itd.
2. U Render → Settings → Custom Domains
3. Slijedi upute za DNS konfiguraciju

---

## 🎉 To je sve!

Sada je tvoja aplikacija sprema za deployment. Sljedeći koraci:

1. **Push kod na GitHub** (`git push`)
2. **Kreiraj Render.com konto**
3. **Slijedi korake u "Korak 3: Deploy na Renderu"**
4. **Čekaj 5-10 minuta** da se build završi
5. **Provjeri** aplikaciju na https://myproperty-ui.onrender.com

**Pitanja?** Vidi `DEPLOYMENT_GUIDE.md` ili Render.com dokumentaciju.

---

*Napravila AI asistentom*  
*Zadnja ažuriranja: 15. maj 2026*
