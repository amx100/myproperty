# 🚀 MyProperty - Deployment na Render.com

## Pregled
- **API**: ASP.NET Core REST sa JWT autentifikacijom
- **UI**: Blazor WebAssembly (SPA)
- **Baza**: PostgreSQL (besplatna na Renderu)
- **Hosting**: Render.com (free tier)

---

## 📋 Koraci za deployment

### 1️⃣ Priprema GitHub repozitorija

#### A. Kreiraj novi GitHub repo
```bash
git init
git add .
git commit -m "Initial commit - ready for deployment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/myproperty.git
git push -u origin main
```

#### B. Struktura koja se očekuje u repu:
```
├── api/
│   └── MyProperty.API/
│       ├── MyProperty.API/
│       ├── Persistence/
│       ├── Presentation/
│       ├── Services/
│       ├── Services.Abstractions/
│       ├── Domain/
│       ├── Contract/
│       ├── Dockerfile
│       └── .dockerignore
├── ui/
│   └── MyProperty.UI/
│       ├── MyProperty.UI/
│       ├── Components/
│       └── Services/
└── render.yaml
```

---

### 2️⃣ Priprema Render.com

#### A. Kreiraj Render.com konto
- Idi na https://render.com
- Registriraj se sa GitHub-om
- Pristupi dashboard-u

#### B. Kreiraj PostgreSQL bazu
1. Klikni **New** → **PostgreSQL**
2. Popuni detalje:
   - **Name**: `myproperty-db`
   - **Region**: `Frankfurt` (ili najbliža lokacija)
   - **PostgreSQL Version**: `16`
3. Klikni **Create Database**
4. **SPREMI**: `Internal Database URL` (trebat će ti za API)

---

### 3️⃣ Deploy API-ja

#### A. Kreiraj web servis
1. Klikni **New** → **Web Service**
2. Povežite GitHub repo:
   - Build command: `cd api/MyProperty.API && dotnet publish -c Release`
   - Start command: `dotnet MyProperty.API.dll`
3. Environment varijable:

```
ASPNETCORE_ENVIRONMENT = Production
ASPNETCORE_URLS = http://+:10000
ConnectionStrings__MainDB = postgresql://user:pass@localhost:5432/real_estate
JWTSettings__securityKey = 7T-d1gfnZ_RqrWVHQYOo8ESZXDwwvPQ8ymWNGqFEm0c
JWTSettings__validIssuer = https://YOUR_API_URL.onrender.com
JWTSettings__validAudience = https://YOUR_UI_URL.onrender.com
```

4. **Advanced** → Custom domain → `myproperty-api.onrender.com`
5. Klikni **Deploy**

---

### 4️⃣ Deploy UI-ja (Blazor WebAssembly)

#### A. Kreiraj static site servis
1. Klikni **New** → **Static Site**
2. Povežite GitHub repo
3. Build command:
   ```
   cd ui/MyProperty.UI/MyProperty.UI && dotnet publish -c Release -o out
   ```
4. Publish directory: `ui/MyProperty.UI/MyProperty.UI/bin/Release/net8.0/publish/wwwroot`
5. Environment: Dodaj varijable ako trebaju

#### B. CORS konfiguracija
Ako dobijaš CORS greške, dodaj headere u UI deployment:

```
Access-Control-Allow-Origin: https://myproperty-api.onrender.com
```

---

## ⚙️ Što se promijenilo u kodu

### API (`Program.cs`)
- **Dinamički izbor baze**: PostgreSQL za production, MySQL za development
- **JWT settings**: Azurirani za production domene
- **Connection string**: Koristi environment varijable

### UI (`ApiEndpoints.cs`)
- **Dinamički API URL**: Production URL na Renderu, localhost za dev
- **Environment detection**: Koristi `RENDER` environment varijablu

---

## 🔑 Environment varijable na Renderu

| Varijabla | Dev | Production |
|-----------|-----|-----------|
| `ASPNETCORE_ENVIRONMENT` | Development | Production |
| `ConnectionStrings__MainDB` | Local MySQL | PostgreSQL URL |
| `JWTSettings__validIssuer` | localhost:5000 | https://api.onrender.com |
| `JWTSettings__validAudience` | localhost:7000 | https://ui.onrender.com |

---

## 🔒 Sigurnost

### ⚠️ Važno: JWT Secret Key
Trenutni secret key je javno dostupan u kodu! Za production:

1. Generiši novi secret key:
   ```powershell
   $key = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes([Guid]::NewGuid().ToString()))
   Write-Host $key
   ```

2. Spremi u Render.com kao environment varijablu (ne u kodu!)

3. Update `appsettings.json` ili koristiti environment varijablu

---

## 🐛 Troubleshooting

### Problem: CORS greške
**Rješenje**: Provjeri `JWTSettings__validAudience` u API varijablama

### Problem: Database connection greška
**Rješenje**: 
- Kopiraj `Internal Database URL` iz PostgreSQL
- Formatiraj kao: `postgresql://user:password@host:port/database`
- Spremi kao `ConnectionStrings__MainDB`

### Problem: Cold start
- Render suspenzuje aplikacije nakon 15 min neaktivnosti
- Pri prvom pozivu trebat će 30+ sekundi da se pokrene
- Za poboljšanje: koristi paid tier ili dodaj health check

### Problem: Build fails
- Provjeri da li su svi `.csproj` fajlovi dostupni
- Provjeri verzije .NET SDK-a
- Koristiti `dotnet restore` prije `publish`

---

## ✅ Checklist za deployment

- [ ] GitHub repo kreiran i kod pushao
- [ ] PostgreSQL baza kreirana na Renderu
- [ ] API deployed sa svim environment varijablama
- [ ] UI deployed sa ispravnim API URL-om
- [ ] CORS konfiguriran
- [ ] JWT settings azurirani
- [ ] Testiran login i osnovne funkcije
- [ ] Custom domains postavljeni
- [ ] SSL certificate generiše se automatski

---

## 📞 Dodatne resurse

- [Render.com dokumentacija](https://render.com/docs)
- [ASP.NET Core na Renderu](https://render.com/docs/deploy-dotnet)
- [Blazor deployment](https://learn.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/webassembly)

---

**Trebaš pomoć?** Kontaktiraj support ili provjeri Render.com dokumentaciju.
