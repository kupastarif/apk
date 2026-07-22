# KupasTarif APK – Root Repository

Orchestrator repository untuk build APK Android dari 3 repository terpisah menggunakan **Git Submodules**.

## 📁 Struktur Repository

```
apk/                        # Root (Orchestrator)
├── .gitmodules             # Konfigurasi submodules
├── .github/workflows/
│   └── build-android.yml   # GitHub Actions workflow
├── package.json
├── capacitor.config.json
├── index.html              # (symlink atau dari spa/ saat build)
├── manifest.json
├── capacitor.config.json
├── sw.js
└── spa/                    # Submodule: UI/Frontend
    ├── index.html
    ├── js/
    ├── assets/
    └── ...
└── engine/                 # Submodule: Business Logic
    ├── 01data.js
    ├── 02valid.js
    ├── ...
    └── 07cache.js
```

## 🚀 Setup & Build

### Prerequisites
- Node.js 24+
- Java 21
- Android SDK

### Local Setup

```bash
# Clone dengan submodules
git clone --recursive https://github.com/kupastarif/apk.git
cd apk

# Atau jika sudah di-clone:
git submodule update --init --recursive

# Install dependencies
npm install
```

### Build APK Locally

```bash
# Persiapan
mkdir public
cp -r spa/assets/ public/
cp -r spa/js/ public/
cp -r engine/ public/
cp spa/index.html public/
cp spa/manifest.json public/
cp spa/sw.js public/

# Capacitor setup
npx cap init KupasTarif kupastarif.gk.app.id --web-dir=public
npx cap add android
npx cap sync android

# Build (requires signing setup)
cd android
./gradlew assembleRelease
```

APK akan tersimpan di: `android/app/build/outputs/apk/release/app-release.apk`

### Build APK via GitHub Actions

Push ke branch `main` atau trigger manual:

```bash
git push origin main
# Atau: GitHub UI → Actions → Build Android APK (Release with Signing) → Run workflow
```

APK akan tersedia di Artifacts setelah build selesai.

## 🔑 GitHub Secrets (untuk Build)

Setup secrets di GitHub repo settings:

```
KEYSTORE_BASE64       # Base64 encoded keystore file
KEYSTORE_PASSWORD     # Keystore password
KEY_ALIAS             # Key alias
KEY_PASSWORD          # Key password
```

Untuk encode keystore:

```bash
base64 -i keystore.jks -o keystore.b64
cat keystore.b64  # Copy ke KEYSTORE_BASE64 secret
```

## 📦 Dependency Management

### Submodules
- `spa/` – Frontend/UI (JavaScript, HTML, CSS)
- `engine/` – Business Logic (Tarif calculation, validation, caching)

Update submodules:

```bash
git submodule update --remote
git commit -am "Update submodules"
git push
```

## 📝 Version Management

Version diambil dari `spa/index.html`:

```html
<script>
  window.APP_VERSION = '2.0.1';
</script>
```

Ubah di satu tempat → Otomatis reflect di APK build.

## 🔧 Struktur Build

Workflow build melakukan:

1. ✅ Checkout dengan submodules recursive
2. ✅ Setup Node.js, Java, Android SDK
3. ✅ Extract version dari `spa/index.html`
4. ✅ Copy files: `spa/` + `engine/` → `public/`
5. ✅ Capacitor sync untuk Android
6. ✅ Configure signing
7. ✅ Build APK release
8. ✅ Upload artifacts

## 🛠️ Troubleshooting

### Submodules tidak terupdate
```bash
git submodule update --init --recursive
```

### Build gagal (gradle error)
```bash
rm -rf android/
rm -rf public/
rm -rf node_modules/
npm install
# Retry build
```

### Version tidak terdeteksi
Pastikan `spa/index.html` memiliki:
```javascript
window.APP_VERSION = 'X.Y.Z';
```

## 📚 Related Repositories

- **spa**: https://github.com/kupastarif/spa
- **engine**: https://github.com/kupastarif/engine

## 📄 License

Lihat LICENSE file di masing-masing submodule.

---

**Created**: July 2026  
**Maintained by**: Kupas⚡Tarif Team
