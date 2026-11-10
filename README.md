# CallVault (Google Drive version)

Android app: call record kore automatic apnar Google Drive-er `CallVault` folder e upload kore.
Apni Drive theke play, rename, delete, folder-e shajiye manage korben.

Gmail password app-e deya jay na. App "Connect Google Drive" chaple Google-er nijer login/consent page khole,
ar app shudhu `drive.file` access pay: mane shudhu nijer banano `CallVault` folder, apnar baki Drive na.

---

## PART A: Google Cloud Console (free, billing/card lagbe na)

1. https://console.cloud.google.com e apnar Gmail diye login korun. Terms ashle accept korun.
2. Upore project selector -> **New project** -> naam `CallVault` -> **Create**. Notun project ta select kore nin.
3. **APIs & Services -> Library** -> search `Google Drive API` -> **Enable**.
4. **Google Auth Platform** (ba **APIs & Services -> OAuth consent screen**) -> **Get started**:
   - App name: `CallVault`, User support email: apnar Gmail
   - Audience: **External**
   - Contact email: apnar Gmail -> agree -> **Create**
5. **Data Access** (ba "Scopes") -> **Add or remove scopes** -> `.../auth/drive.file` tick kore **Update -> Save**.
6. **Audience** e jan:
   - Prothome **Test users -> Add users** -> apnar Gmail add korun.
   - Test hoye gele **Publish app** (In production) chapun. Na korle Google 7 din por login expire kore dey,
     tokhon abar Connect chapte hobe. `drive.file` sensitive scope na, tai verification lagbe na;
     "unverified app" warning ashle **Advanced -> Go to CallVault** chapun.
7. SHA-1 ber korun (niche PART B-te). Tarpor **Clients -> Create client**:
   - Application type: **Android**
   - Name: `CallVault Android`
   - Package name: `com.callvault`
   - SHA-1 certificate fingerprint: PART B theke paoya SHA-1
   - **Create**. (Code e kono client ID boshate hoy na.)

## PART B: Build ar SHA-1

1. Android Studio install korun (https://developer.android.com/studio).
2. `File -> Open` diye `callvault` folder ta open korun. Gradle sync-e Android SDK 34 download korte bolle **Install** chapun.
   Gradle-er jonne error ashle `File -> Settings -> Build -> Gradle` e **Use Gradle from: 'gradle-wrapper.properties'** select korun.
3. **SHA-1** ber korar upay (jekono ekta):
   - Terminal/CMD e (Windows e `%USERPROFILE%\.android\debug.keystore`):
     `keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android`
     (`keytool` Android Studio-r `jbr/bin` folder e thake). Output-er `SHA1:` line ta nin.
   - Ba Android Studio-r Gradle panel: `app -> Tasks -> android -> signingReport`.
   - Ekbar debug build chalaleo (Run) keystore toiri hoy.
4. Ei SHA-1 ta PART A-r step 7 e boshan. **Je computer e build korben, oi computer-er SHA-1 e dite hobe.**
   Onno computer e build korle ba release key use korle notun SHA-1 diye arekta Android client banate hobe.

## PART C: Phone e install

1. Phone: `Settings -> About phone -> Build number` 7 bar chhupe Developer options on korun, tarpor **USB debugging** on.
2. USB diye phone connect kore Android Studio te **Run** chapun. Ba `Build -> Build APK(s)` diye APK phone e pathiye install (Unknown sources allow korte hobe).
3. Phone e Google Play Services thaka dorkar (beshirbhag phone e ache).

## PART D: App-e proothom bar setup (button 1 theke 5)

1. **Permission din**: mic, phone, call log, notification: shob Allow.
2. **Google Drive connect**: Gmail select korun, consent-e Allow. "Google Drive connected" dekhabe.
3. **Notification access**: CallVault on korun (WhatsApp/Messenger call dhorar jonne).
4. **Accessibility**: CallVault on korun (kono kono phone e call-er shomoy mic access rakhe).
5. **Battery optimization**: CallVault ke "Unrestricted"/bad din. Xiaomi/Oppo/Vivo/Samsung e **Autostart** o on korun,
   ar recent apps e CallVault **lock** korun.

## PART E: Test (khub dorkari)

1. Nijer arekta number e call korun, kichukkhon kotha bolun, speaker on rakhun, call kete din.
2. 1-2 minute por Drive -> `CallVault` folder e `.m4a` file ashche ki na dekhun. Play kore dekhun duipokkher awaj ashche ki na.
3. Awaj ashe na hole: speakerphone try korun, ar accessibility on ache ki na dekhun. Phone model onujayi kono kono phone e onno pashter awaj ashe na.
4. WhatsApp/Messenger call diye abar test korun.

## Recording notification

Main screen e "Show recording notification" checkbox tick thakle call recording-er shomoy
CallVault notification low-noise bhabe visible thakbe. Untick korle Android-er minimum
visibility channel use hobe. Android microphone foreground service-er jonno notification
required, tai app notification puro remove korte pare na. Android-er app notification settings
theke user channel-er behavior aro customize korte paren.

Ei checkbox shudhu nijer device-e notification dekhano niyontron kore; eta call-er onno
participant-er consent-er bodole noy. Recording-er age onno pokkhoke janan ebong proyojonio
consent nin.

## App icon lukano (Hide app)

Main screen e "Hide app" checkbox tick korun. Tick thakle, app theke ber hoye gele (home/back chaple) app-er icon
app drawer o home screen theke lukiye jabe. App nijei chalte thakbe (call record, upload, shob age-er motoi).

Abar khulte: phone-er nijer Dialer app khule `*#*#8225#*#*` type korun (call button chapte hobe na, type korlei
khule jabe). Eta kaj korle MainActivity abar khule jabe ar icon o ferot ashbe. **Shob phone/dialer e eta kaj nao
korte pare** (Xiaomi/Oppo/Vivo-r kichu custom dialer e secret code block kora thake) — shekhetre `adb` diye
command chaliye icon ferot anun:

```
adb shell pm enable com.callvault/.LauncherAlias
```

Untick korle sathe sathe icon ferot ashe.

## Limitation

- App mic diye record kore, tai onno pashter awaj phone model-er upor nirbhor kore.
- Recording-er shomoy software gain (GainRecorder.kt, MAX_GAIN) ar Android AGC on thake, jate speaker chhara-o onno pashter awaj jore shona jay. Awaj aro boro/kom korte GainRecorder.kt-er `MAX_GAIN`/`TARGET_PEAK` bodlan.
- Outgoing SIM call-er number kono phone e `unknown` thake.
- WhatsApp/Messenger start-e 1-2 second miss hote pare.
- Phone update-er por abar test korun.
- Internet na thakle file phone-e queue te thake, ashle upload hoy.

## Legal

Call-er shuru-te onno pokkhoke janiye din je call record hochhe.
