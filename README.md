# Event Collection (Android)

Kotlin + Jetpack Compose + Room. Offline-first, no network permission, no server.

## Build
1. Open this folder in Android Studio (Koala 2024.1+ / JDK 17). Let Gradle sync.
2. Debug APK: `Build > Build APK(s)` or `./gradlew assembleDebug`
   -> `app/build/outputs/apk/debug/app-debug.apk`
   (If `./gradlew` is missing, run `gradle wrapper --gradle-version 8.7` once, or just use Android Studio.)

## Release APK
1. Create a keystore (keep it safe, you need it for every update):
   `keytool -genkeypair -v -keystore event-release.jks -alias event -keyalg RSA -keysize 2048 -validity 10000`
2. Create `keystore.properties` in the project root:
   ```
   storeFile=/full/path/event-release.jks
   storePassword=...
   keyAlias=event
   keyPassword=...
   ```
3. `./gradlew assembleRelease` -> `app/build/outputs/apk/release/app-release.apk`
   (Or Android Studio: Build > Generate Signed Bundle / APK.)

## WhatsApp behaviour
- Uses Android's standard share intent targeted at WhatsApp (or WhatsApp Business) with the PDF via FileProvider
  and the message text as caption. The collector taps Send.
- WhatsApp's documented intent does NOT pre-select a recipient. By default the app copies +91XXXXXXXXXX to the
  clipboard so it can be pasted into WhatsApp's search box. (Only works for numbers WhatsApp can find in
  contacts/chats.)
- `Share.USE_JID_EXTRA` enables the undocumented `jid` extra that many apps use to pre-select the chat.
  It is off because it is not an official API and can stop working at any time.
- Fully automatic sending requires the WhatsApp Business Platform (Cloud API) and a backend. Not included.

## Manual test checklist
Add > Save > receipt EVT-000001 created > PDF generated > WhatsApp opens with the PDF attached > Records shows it >
Summary totals updated. Repeat with WhatsApp uninstalled or by pressing Back in WhatsApp: the record must remain.
