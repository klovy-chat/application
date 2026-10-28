# application

[![License: Klovy](https://img.shields.io/badge/License-Klovy-blue.svg)](LICENSE)

The official desktop app of Klovy Chat.

Oficjalna aplikacja desktopowa komunikatora **Klovy Chat** — opakowanie [app.klovy.chat](https://app.klovy.chat) w [Tauri 2](https://tauri.app).

Wspierane platformy: Windows, macOS, Linux. Mobile (Android / iOS) nie jest częścią tego projektu.

---

## O projekcie

Desktop ładuje ten sam frontend co przeglądarka (`https://app.klovy.chat`). Native warstwa dodaje instalatory, Discord Rich Presence i integracje ze sklepami (Microsoft Store, Mac App Store).

| Platforma | Dystrybucja |
|-----------|-------------|
| Windows | `.msi` / `.exe` (NSIS), [Microsoft Store](docs/STORE_RELEASE.md#windows--microsoft-store) |
| macOS | `.dmg`, [Mac App Store](docs/STORE_RELEASE.md#macos--app-store) |
| Linux | `.deb`, `.AppImage`, `.rpm` — [mapa dystrybucji](docs/STORE_RELEASE.md#linux--wiele-dystrybucji) |

### Ekosystem

| Repo | Rola |
|------|------|
| [backend](https://github.com/klovy-chat/backend) | API i WebSocket |
| [frontend](https://github.com/klovy-chat/frontend) | Aplikacja web (`app.klovy.chat`) |
| [website](https://github.com/klovy-chat/website) | Strona (`klovy.chat`) |
| [application](https://github.com/klovy-chat/application) | Desktop (Tauri) |

---

## Funkcje

- Okno natywne z aplikacją webową Klovy Chat
- Discord Rich Presence
- Instalatory per platforma i warianty sklepowe

---

## Wymagania

- **Node.js** 18+
- **Rust** ([rustup](https://rustup.rs))
- **Windows 10 (version 1803+) / 11:** MSVC Build Tools, WebView2 Runtime (dev). Windows 95, 98, 2000, XP, Vista, 7, 8 and 8.1 are not supported by Tauri 2.
- **macOS:** Xcode (build + opcjonalnie App Store)
- **Linux:** `libwebkit2gtk-4.1-dev`, `libayatana-appindicator3-dev`, `librsvg2-dev`

---

## Uruchomienie lokalne

```bash
git clone https://github.com/klovy-chat/application.git
cd application
npm install
```

```bash
npm run dev      # dev na bieżącym OS
npm run build    # instalatory dla bieżącego OS
```

---

## Buildy per platforma

```bash
npm run build:windows          # MSI + NSIS (dystrybucja bezpośrednia)
npm run build:windows:store    # MSI/NSIS z offline WebView2 → Microsoft Store
npm run build:macos            # DMG universal (Intel + Apple Silicon)
npm run build:macos-appstore   # .app bundle → Mac App Store
npm run build:linux            # deb + rpm + AppImage
```

Szczegóły sklepów, silent install, podpisywanie: [docs/STORE_RELEASE.md](docs/STORE_RELEASE.md).

Ikony:

```bash
npm run icons:generate
```

---

## Konfiguracja

| Plik | Opis |
|------|------|
| `src-tauri/tauri.conf.json` | Główna konfiguracja desktop |
| `src-tauri/tauri.windows.conf.json` | Override Windows (merge przy buildzie) |
| `src-tauri/tauri.macos.conf.json` | Override macOS |
| `src-tauri/tauri.microsoftstore.conf.json` | WebView2 offline → Microsoft Store |
| `src-tauri/capabilities/` | Uprawnienia Tauri (`linux`, `macOS`, `windows`) |

Discord Rich Presence: `src-tauri/tauri.conf.json` → `plugins.discordPresence`.

---

## CI i wydania

GitHub Actions (oficjalne repo):

- [`.github/workflows/build.yml`](.github/workflows/build.yml) — artefakty Windows / macOS / Linux
- [`.github/workflows/release.yml`](.github/workflows/release.yml) — tagi i instalatory

Wydanie z `main` (utrzymujący):

```powershell
npm run release          # 1.0.0 → 1.0.1
npm run release:minor    # 1.0.1 → 1.1.0
```

Albo GitHub → **Actions → Release → Run workflow**. CI podbija wersję, taguje i publikuje instalatory. Aplikacja nie aktualizuje się automatycznie; nową wersję desktop należy pobrać i zainstalować ręcznie. Backend porównuje wersję aplikacji z najnowszym opublikowanym GitHub Release i blokuje żądania HTTP API ze starszego desktopu.

Frontend jest ładowany bezpośrednio z `https://app.klovy.chat`, więc zmiany w opublikowanej aplikacji webowej są widoczne bez aktualizacji desktopu. Zmiany w natywnej powłoce wymagają wydania i instalacji nowego instalatora. Sam push do GitHub nie publikuje Release; blokowanie starszych wersji zaczyna działać po wydaniu nowej wersji. Backend musi mieć dostęp wychodzący do GitHub Releases.

Przy wdrażaniu wymuszania wersji zachowaj kolejność: najpierw backend, potem nowy desktop Release (z komendą odczytu wersji), a na końcu frontend wysyłający wersję desktopu. Backend buforuje najnowszy Release przez maksymalnie 5 minut.

Wersje desktop są publikowane w GitHub Releases.

---

## Technologie

- **Tauri 2** — powłoka desktop
- **Rust** — native (Discord)
- **Node.js** — CLI Tauri i skrypty wydania

---

## Struktura projektu

```
application/
├── src-tauri/
│   ├── src/                 # Rust: discord_presence
│   ├── capabilities/        # Uprawnienia okna
│   ├── tauri.conf.json      # Konfiguracja główna
│   └── icons/
├── scripts/                 # bump-version, dispatch-release
├── docs/                    # STORE_RELEASE.md
├── .github/workflows/       # build + release
└── package.json
```

---

## Contributing

Kod jest publiczny na [Klovy License](LICENSE). Issue i pull requesty są mile widziane.

1. Zrób [fork](https://github.com/klovy-chat/application/fork)
2. Utwórz branch: `git checkout -b feature/opis-zmiany`
3. Commit (bez sekretów i kluczy podpisu)
4. Otwórz pull request do `main`

Opisz w PR **co** i **dlaczego**. Poprawki docs i buildów per platforma też są OK.

---

## Bezpieczeństwo

Luki zgłaszaj prywatnie przez [GitHub Security Advisories](https://github.com/klovy-chat/application/security/advisories/new). Nie otwieraj publicznego issue z exploitami.

---

## Licencja

Kod jest udostępniony na **[Klovy License](LICENSE)** — użycie osobiste, edukacyjne i niekomercyjne. Dystrybucja komercyjna, konkurencyjny komunikator oraz użycie marek Klovy wymagają pisemnej zgody Jakuba Maksymowicza. Zgłoszenie PR, błędu lub audytu bezpieczeństwa oznacza zgodę na warunki kontrybucji z licencji (pkt 7–11).

© 2026 [Jakub Maksymowicz](https://github.com/Klovy06)
