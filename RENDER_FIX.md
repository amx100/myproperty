# 🔧 FIX - Render.com Deployment (Ispravljeno)

## ❌ Šta je Pošlo Naopako

Render je detektovao Node.js umjesto .NET-a. Problem je bio u `render.yaml` formatu.

---

## ✅ ISPRAVLJENI Plan - Korak po Korak

### 📋 Opcija 1: Koristi Render Dashboard (PREPORUČENO)

#### A. Deploy PostgreSQL

1. Idi na https://render.com/dashboard
2. **New** → **PostgreSQL** (klikni na + ili New button)
3. Popuni:
   - **Name**: `myproperty-db`
   - **Database**: `real_estate`
   - **User**: `postgres`
   - **Region**: `Frankfurt` (ili izberi svoju)
4. Klikni **Create Database**
5. **SPREMI**: Cijeli connection string (trebat će ti za API)

---

#### B. Deploy API-ja - ISPRAVNO

1. **New** → **Web Service**
2. **Connect a repository** → Odaberi `myproperty` repo
3. Popuni:
   - **Name**: `myproperty-api`
   - **Environment**: Odaberi iz dropdown-a **Dot NET** (važno!)
   - **Build Command**: 
     ```
     cd api/MyProperty.API && dotnet publish -c Release
     ```
   - **Start Command**: 
     ```
     dotnet api/MyProperty.API/MyProperty.API/bin/Release/net8.0/MyProperty.API.dll
     ```

4. **Environment Variables** → Klikni **Add Environment Variable**:

```
ASPNETCORE_ENVIRONMENT = Production
ASPNETCORE_URLS = http://+:10000
ConnectionStrings__MainDB = postgresql://USERNAME:PASSWORD@HOSTNAME:PORT/DATABASE
JWTSettings__securityKey = 7T-d1gfnZ_RqrWVHQYOo8ESZXDwwvPQ8ymWNGqFEm0c
JWTSettings__validIssuer = https://myproperty-api.onrender.com
JWTSettings__validAudience = https://myproperty-ui.onrender.com
JWTSettings__expiryInMinutes = 10
```

5. **Advanced** → Root Directory: `api/MyProject.API`
6. Klikni **Create Web Service**

---

#### C. Deploy UI-ja

1. **New** → **Static Site**
2. **Connect repository** → `myproperty`
3. Populi:
   - **Name**: `myproperty-ui`
   - **Build Command**:
     ```
     cd ui/MyProperty.UI/MyProperty.UI && dotnet publish -c Release -o dist
     ```
   - **Publish Directory**:
     ```
     ui/MyProperty.UI/MyProperty.UI/bin/Release/net8.0/publish/wwwroot
     ```

4. Klikni **Create Static Site**

---

## 🔑 Gdje Pronaći Connection String za PostgreSQL?

1. U Render dashboard, klikni na `myproperty-db` (baza)
2. Vidi **Internal Database URL** (nešto slično):
   ```
   postgresql://postgres:XXXXXXXX@dpg-xxx.render.internal:5432/real_estate
   ```
3. To je tvoj `ConnectionStrings__MainDB` value!

---

## 🚨 VAŽNO: Prije nego što deploy-aš

### Provjeri:
- [ ] GitHub repo je pushao sa svim fajlovima
- [ ] render.yaml je ažuriran (ili ga ignoriraj i koristi Dashboard)
- [ ] PostgreSQL baza je kreiirana sa connection stringom

### Ako koristiš Dashboard (preporučeno):
- ✅ Ignoriraj render.yaml
- ✅ Sve se radi kroz web interface

### Ako koristiš render.yaml:
- [ ] Fajl mora biti u root direktoriju
- [ ] Format mora biti ispravan
- [ ] Trebaje: `runtime: dotnet-8` (nije `env: dotnet`)

---

## 🆘 Ako Deploy Opet Padne

### ❌ "dotnet: command not found"
**Razlog**: Render nije prepoznao .NET okruženje  
**Rješenje**: 
- U Dashboard → Provjeri da je **Environment** postavljen na **Dot NET**
- Ili dodaj `runtime.txt` sa sadržajem: `dotnet-8`

### ❌ "File not found"
**Razlog**: Build command nije ispravan  
**Rješenje**:
```
# Provjeri:
cd api/MyProperty.API
dotnet restore
dotnet publish -c Release
# Vidi gdje je output
```

### ❌ "Database connection failed"
**Razlog**: Connection string nije ispravan  
**Rješenje**:
- Kopiraj **Internal Database URL** iz PostgreSQL servisa
- Format mora biti: `postgresql://user:pass@host:port/database`

---

## 📊 Brza Provjera - Što je Gdje?

```
Render Dashboard (https://render.com/dashboard)
├── myproperty-db (PostgreSQL)
│   ├── Connection String ← Kopiraj za API
│   └── Status: Available
├── myproperty-api (Web Service)
│   ├── Build Command: cd api/MyProperty.API && dotnet publish -c Release
│   ├── Start Command: dotnet api/MyProperty.API/MyProperty.API/bin/Release/net8.0/MyProperty.API.dll
│   ├── Environment: Dot NET ← VAŽNO!
│   └── Env Variables: ← SVI su dodani
└── myproperty-ui (Static Site)
    ├── Build Command: cd ui/MyProperty.UI/MyProperty.UI && dotnet publish -c Release -o dist
    ├── Publish Directory: ui/MyProperty.UI/MyProperty.UI/bin/Release/net8.0/publish/wwwroot
    └── Status: Deploying...
```

---

## ✅ Sljedeće Akcije

1. **Push** koda na GitHub (ako nije već):
   ```powershell
   cd d:\Projekti\myproperty
   git add render.yaml
   git commit -m "Fix render.yaml format"
   git push origin main
   ```

2. **Odjavi se** sa Render-a i ponovo se prijavi da osvježi cache

3. **Kreiranje servisa** - korak po korak:
   - Prvo: PostgreSQL baza
   - Drugo: API (sa connection stringom iz baze)
   - Treće: UI

4. **Čekaj** 10-15 minuta da se build završi

5. **Provjeri** Render logs ako nešto ne radi

---

## 🎯 TL;DR - Brzo Rješenje

**Dashboard pristup je sigurniji:**
1. Odjavi se sa Render-a
2. Ponovo se prijavi
3. Delete sve servise
4. Kreiraj nanovo preko **Dashboard** (ne render.yaml)
5. Deploy će raditi ✅

---

*Zadnja ažuriranja: 15. maj 2026*
