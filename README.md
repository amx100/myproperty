# 📢 MyProperty - README za GitHub

## 🎯 O Projektu

**MyProperty** je moderan web aplikacija za upravljanje nekretninama sa:
- **REST API** - ASP.NET Core sa JWT autentifikacijom
- **UI** - Blazor WebAssembly sa MudBlazor komponentama
- **Baza** - PostgreSQL (production) / MySQL (development)

## 🏗️ Arhitektura

```
MyProperty/
├── api/              # ASP.NET Core REST API
│   └── MyProperty.API/
│       ├── Contract/           # DTOs
│       ├── Domain/             # Entities
│       ├── Persistence/        # EF Core
│       ├── Presentation/       # Controllers
│       ├── Services/           # Business logic
│       └── Services.Abstractions/
├── ui/               # Blazor WebAssembly
│   └── MyProperty.UI/
│       ├── Components/         # Reusable components
│       ├── Pages/              # Routes/Pages
│       └── Services/           # API clients
└── Documentation/
    ├── QUICK_START.md
    ├── DEPLOYMENT_GUIDE.md
    ├── DATABASE_MIGRATION.md
    └── ARCHITECTURE.md
```

## 🚀 Quick Start

### Lokalni razvoj
```powershell
# API
cd api\MyProperty.API
dotnet restore
dotnet run

# UI (u drugom terminalu)
cd ui\MyProperty.UI\MyProperty.UI
dotnet restore
dotnet run
```

Pristupite na:
- **API**: https://localhost:5000/swagger
- **UI**: https://localhost:7000

### Deployment na Renderu
Vidi [QUICK_START.md](./QUICK_START.md)

## 📋 Tech Stack

### Backend
- **.NET 8.0** - Framework
- **ASP.NET Core** - REST API
- **Entity Framework Core** - ORM
- **PostgreSQL/MySQL** - Database
- **JWT** - Authentication

### Frontend
- **Blazor WebAssembly** - UI framework
- **MudBlazor** - UI components
- **Blazored.LocalStorage** - Client storage

## 🔑 Ključne Značajke

✨ **Autentifikacija**
- JWT tokensi
- Email potvrda
- Password reset

🏠 **Upravljanje Nekretninama**
- CRUD operacije
- Slike nekretnina
- Search & filter

📅 **Rezervacije**
- Booking system
- Datumi dostupnosti
- Status tracking

👥 **Korisnici**
- Role-based access control
- Profile management
- Account settings

## 🔧 Konfiguracija

### Development (appsettings.Development.json)
```json
{
  "ConnectionStrings": {
    "MainDB": "Server=localhost;Port=3306;Database=real_estate;User=root;Password="
  },
  "JWTSettings": {
    "securityKey": "...",
    "validIssuer": "localhost:5000",
    "validAudience": "localhost:7000",
    "expiryInMinutes": 10
  }
}
```

### Production (Environment Variables)
```
ASPNETCORE_ENVIRONMENT=Production
ConnectionStrings__MainDB=postgresql://user:pass@host/database
JWTSettings__securityKey=...
JWTSettings__validIssuer=https://api.onrender.com
JWTSettings__validAudience=https://ui.onrender.com
```

## 📚 Dokumentacija

| Dokument | Opis |
|----------|------|
| [QUICK_START.md](./QUICK_START.md) | 📘 Brz početak |
| [DEPLOYMENT_GUIDE.md](./DEPLOYMENT_GUIDE.md) | 🚀 Deployment upute |
| [DATABASE_MIGRATION.md](./DATABASE_MIGRATION.md) | 🔄 Migration guide |
| [ARCHITECTURE.md](./api/MyProperty.API/ARCHITECTURE.md) | 🏗️ Arhitektura |

## 🛠️ Development

### Prerequisiti
- .NET 8.0 SDK
- Visual Studio Code ili Visual Studio 2022
- PostgreSQL/MySQL
- Git

### Setup
```powershell
# Kloniraj repo
git clone https://github.com/YOUR_USERNAME/myproperty.git
cd myproperty

# Restore NuGet pakete
cd api\MyProperty.API
dotnet restore

cd ..\..\ui\MyProperty.UI
dotnet restore

# Update baze
cd ..\..\api\MyProperty.API
dotnet ef database update -p Persistence -s MyProperty.API
```

## 📝 Contributing

1. Fork repo
2. Kreiraj feature granu (`git checkout -b feature/AmazingFeature`)
3. Commit promjene (`git commit -m 'Add AmazingFeature'`)
4. Push na granu (`git push origin feature/AmazingFeature`)
5. Open Pull Request

## 📄 License

Ovaj projekat je dostupan pod MIT licencom.

## 👤 Autor

**MyProperty Team**
- GitHub: [@yourusername](https://github.com/yourusername)

## 🙏 Acknowledgments

- [MudBlazor](https://www.mudblazor.com/) - UI Components
- [Blazored](https://github.com/Blazored) - Blazor Libraries
- [Render.com](https://render.com) - Free Hosting

## 📞 Support

Za pitanja ili problem:
1. Provjeri [Discussions](../../discussions)
2. Kreiraj [Issue](../../issues)
3. Pročitaj [Dokumentaciju](./DEPLOYMENT_GUIDE.md)

---

**Made with ❤️ by MyProperty Team**

*Zadnja ažuriranja: 15. maj 2026*
