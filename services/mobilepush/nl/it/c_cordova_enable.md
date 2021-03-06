---

copyright:
 years: 2015, 2016

---

# Abilitazione delle applicazioni Cordova alla ricezione di notifiche di push
{: #cordova_enable}
Ultimo aggiornamento: 17 ottobre 2016
{: .last-updated}

Cordova è una piattaforma per creare applicazioni ibride con JavaScript, CSS e HTML. Il {{site.data.keyword.mobilepushshort}} supporta lo sviluppo di applicazioni Android e iOS basate su Cordova.

Puoi abilitare le applicazioni Cordova a ricevere le notifiche di push ai tuoi dispositivi.

## Installazione del plugin push Cordova
{: #cordova_install}

Installa e utilizza il plugin di Push client per sviluppare ulteriormente le tue applicazioni Cordova. Questo installa anche il plugin Cordova core, che inizializza la tua connessione a Bluemix.

### Prima di cominciare

1. Scarica le ultime versioni dell'SDK Android Studio e Xcode.
1. Configura il tuo emulatore. Per Android Studio, utilizza un emulatore che supporti l'API Google Play.
1. Installa lo strumento della riga di comando Git. Per Windows, assicurati di selezionare l'opzione **Esegui Git dal prompt dei comandi della finestra**. Per informazioni su come scaricare e installare questo strumento, consulta [Git](https://git-scm.com/downloads).
1. Installa lo strumento Node.js e il gestore pacchetti di nodi (NPM). Lo strumento della riga comandi NPM è integrato con Node.js. Per informazioni su come scaricare e installare Node.js, consulta [Node.js](https://nodejs.org/en/download/).
1. Dalla riga comandi, installa gli strumenti della riga di comando Cordova utilizzando il comando **npm install -g cordova**. È necessario per utilizzare il plugin Push Cordova. Per informazioni su come installare Cordova e configurare la tua applicazione Cordova, consulta [Cordova Apache](https://cordova.apache.org/#getstarted). Per ulteriori informazioni, vedi il file readme del [plug-in Cordova push](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-cordova-plugin-push).
1. Passa alla cartella in cui vuoi creare la tua applicazione Cordova ed esegui il seguente comando per
           creare un'applicazione Cordova. Se hai un'applicazione Cordova esistente, vai al passo 3.
```
cordova create your_app_name
	cd your_app_name
```
	{: codeblock}
- Facoltativo: puoi modificare il file **config.xml** e modificare il nome dell'applicazione nell'elemento <name> in uno a tua scelta, invece del nome predefinito HelloCordova.

Assicurati di specificare l'ID bundle corretto. Il seguente messaggio di errore potrebbe essere in Xcode, se viene specificato un ID bundle non corretto.

* L'eseguibile è stato firmato con diritti non validi.
* I diritti specificati nel tuo file Diritti di firma del codice non corrispondo a quelli specificati nel tuo profilo di provisioning. Per risolvere questo problema, specifica l'ID bundle corretto in Xcode o nel tuo file **config.xml** dell'applicazione Cordova.

1. Aggiungi la API supportata minima o la dichiarazione di destinazione della distribuzione al file config.xml per la tua applicazione Cordova. Il valore minSdkVersion deve essere maggiore di 15. Il valore targetSdkVersion deve sempre riflettere l'SDK Android più recente disponibile da Google.
	
	* Android - Con il tuo editor, apri il file config.xml e aggiorna l'elemento
`<platform name="android">` con le versioni SDK minima e di destinazione:

```
< !-- add deployment target declaration --> 
add deployment target declaration <preference name="android-minSdkVersion" value="15" />
  <preference name="android-targetSdkVersion" value="23" />
</platform>
```
    {: codeblock}

   * iOS - Aggiorna l'elemento <platform name="ios"> con una dichiarazione di destinazione della distribuzione:

```
<platform name ="ios">
<preference name=deployment-target" value="8.0" /> <!-- other properties -->
</ platform>
```
	{: codeblock}

1. Dalla CLI (command-line interface) Cordova, aggiungi le tue piattaforme: iOS, Android o entrambe utilizzando il comando:
```
cordova platform add ios
	cordova platform add android
```
	{: codeblock}

1. Dalla directory root della tua applicazione Cordova, immetti il seguente comando per installare il plugin Cordova push: **cordova plugin add ibm-mfp-push**. A seconda delle piattaforme da te aggiunte, potresti visualizzare:
```
Installing "ibm-mfp-push" for android
	Installing "ibm-mfp-push" for ios
```
	{: codeblock}

1. Da *your-app-root-folder*, verifica che i plugin Cordova core e push siano stati installati correttamente utilizzando questo comando: **cordova plugin list**. A seconda delle piattaforme da te aggiunte, potresti visualizzare:
```
ibm-mfp-core 1.0.0 "MFPCore"
	ibm-mfp-push 1.0.0 “MFPPush"
```
	{: codeblock}

1. (solo iOS) - Configura il tuo ambiente di sviluppo iOS.
2. Completa i seguenti passi secondari:

 a. Apri il tuo file your-app-name.xcodeproj nella directory *your-app-name***/platforms/ios** con Xcode.

 b. Aggiungi l'intestazione di collegamento. Vai a **Build settings > Swift Compiler - Code Generation > Objective-C Bridging Header** e aggiungi il seguente percorso: *your-project-name***/Plugins/ibm-mfp-core/Bridging-Header.h**

 c. Aggiungi il parametro Frameworks. Vai a **Build Settings > Linking > Runpath Search Paths** e aggiungi il parametro `@executable_path/Frameworks`.

 d. Rimuovi il commento per le seguenti istruzioni di importazione push nella tua intestazione di collegamento. Vai a *your-project-name***/Plugins/ibm-mfp-core/Bridging-Header.h**

```
//#import <IMFPush/IMFPush.h>
	//#import <IMFPush/IMFPushClient.h>
	//#import <IMFPush/IMFResponse+IMFPushCategory.h>
```
	{: codeblock}

 e. Crea ed esegui la tua applicazione con Xcode.

1. (solo Android)- Crea il tuo progetto Android utilizzando il seguente comando:
**cordova build android**.

	**Nota**: prima di aprire il tuo progetto in Android Studio, devi creare la tua applicazione Cordova con la CLI di Cordova. Questo aiuterà nel prevenire errori di generazione.


## Inizializzazione del plugin Cordova
{: #cordova_initialize}

Prima di poter utilizzare il plugin Cordova del servizio {{site.data.keyword.mobilepushshort}}, devi inizializzarlo passando la rotta e il GUID dell'applicazione. Dopo che hai inizializzato il plugin, puoi stabilire una connessione all'applicazione server che hai creato nel dashboard Bluemix. Il plugin Cordova è il contenitore per gli SDK client Android e iOS per abilitare un'applicazione Cordova a comunicare con i servizi Bluemix.

1. Inizializza il BMSClient copiando e incollando il seguente frammento di codice nel tuo file JavaScript principale (normalmente ubicato nella directory **www/js**).

```
BMSClient.initialize("https://myapp.mybluemix.net","App GUID");
```
	{: codeblock}

1. Modifica il frammento di codice per utilizzare la tua rotta Bluemix e i parametri appGUID. Fai clic sul link **Opzioni mobili** nel tuo dashboard Push per ottenere rotta e GUID dell'applicazione e il segreto client. Utilizza i valori rotta e GUID dell'applicazione come tuoi parametri nel tuo frammento di codice `BMSClient.initialize`.

	**Nota**: se hai creato un'applicazione Cordova utilizzando la CLI di Cordova, ad esempio, con il comando Cordova create app-name, inserisci questo codice Javascript nel file **index.js**, dopo la funzione `app.receivedEvent` nella funzione `onDeviceReady: function()` per inizializzare il client BMS.

```
onDeviceReady: function() {
app.receivedEvent('deviceready');
BMSClient.initialize("https://myapp.mybluemix.net","App GUID");
    },
```
	{: codeblock}

## Registrazione di dispositivi
{: #cordova_register}


Per registrare un dispositivo con il servizio {{site.data.keyword.mobilepushshort}}, richiama il metodo di registro. Copia il seguente frammento di codice nella tua applicazione Cordova per registrare un dispositivo.

```
var success = function(message) { console.log("Success: " + message); };
	var failure = function(message) { console.log("Error: " + message); };
	MFPPush.registerDevice({}, success, failure);
```
	{: codeblock}

### Android
{: #cordova_register_android}
Android non utilizza il parametro delle impostazioni. Se stai soltanto creando un'applicazione Android, invia un oggetto vuoto. Ad esempio:

```
MFPPush.registerDevice({}, success, failure);
	MFPPush.unregisterDevice(success, failure);
```
	{: codeblock}

### iOS
{: #cordova_register_ios}
Per personalizzare le proprietà di avviso, badge e audio, aggiungi il seguente frammento di codice JavaScript alla parte web della tua applicazione Cordova.

```
var settings = {
   ios: {
      alert: true,
             badge: true,
             sound: true
         }
}
	MFPPush.registerDevice(settings, success, failure);
```
	{: codeblock}


### JavaScript
{: #cordova_register_js}

```
MFPPush.registerDevice({}, success, failure);
```
	{: codeblock}

Puoi accedere ai contenuti del parametro di risposta con esito positivo in Javascript utilizzando JSON.parse:
**var token = JSON.parse(response).token**


Le chiavi disponibili sono: `token` e `deviceId`.

Il seguente frammento di codice JavaScript mostra come inizializzare il tuo Bluemix Mobile Services client SDK, registra un dispositivo con il servizio {{site.data.keyword.mobilepushshort}} e ascolta le notifiche push. Inserisci questo codice nel tuo file Javascript.

```
//Registra il token di dispositivo presso il Bluemix Push Notification Service
funcapplication(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData){
  CDVMFPPush.sharedInstance().didRegisterForRemoteNotifications(deviceToken)
}
```
	{: codeblock}

```
//Gestisci l'errore in caso di registrazione del token di dispositivo presso APNS non riuscita
funcapplication(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSErrorPointer){
CDVMFPPush.sharedInstance().didFailToRegisterForRemoteNotifications(error)
}
```
	{: codeblock}
in **onDeviceReady: function()**.

```
onDeviceReady: function() {
app.receivedEvent('deviceready');
     BMSClient.initialize("https://http://myroute_mybluemix.net","my_appGuid");
     var success = function(message) { console.log("Success: " + message); };
     var failure = function(message) { console.log("Error: " + message); };
     var settings = {
     ios: {
         alert: true,
             badge: true,
             sound: true
         }
  };
   MFPPush.registerDevice(settings, success, failure);
   var notification = function(notif){
       alert (notif.message);
    };
    MFPPush.registerNotificationsCallback(notification);
	 }
```
	{: codeblock}

### Objective-C
{: #cordova_register_objective}
Aggiungi il seguente frammento di codice Objective-C alla classe delegato della tua applicazione

```
// Registra il token dispositivo con Bluemix Push Notification Service
	- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  [[CDVMFPPush sharedInstance] didRegisterForRemoteNotifications:deviceToken];
	}
// Gestisci l'errore in caso di registrazione del token di dispositivo presso APNS non riuscita
	- (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error {
   [[CDVMFPPush sharedInstance] didFailToRegisterForRemoteNotificationsWithError:error];
	}
```
	{: codeblock}

###Swift
{: #cordova_register_swift}
Aggiungi il seguente frammento di codice Swift alla classe delegato della tua applicazione.

```
funcapplication(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData){
   CDVMFPPush.sharedInstance().didRegisterForRemoteNotifications(deviceToken)
}
//Gestisci l'errore in caso di registrazione del token di dispositivo presso APNS non riuscita
funcapplication(application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: NSErrorPointer){
CDVMFPPush.sharedInstance().didFailToRegisterForRemoteNotifications(error)
}
```
	{: codeblock}

##Passi successivi

{: #cordova_register_next}

Crea il tuo progetto e quindi eseguilo utilizzando i seguenti comandi:

####Android
{: android-next-steps}

```
cordova build android
```
	{: codeblock}

```
cordova run android
```
	{: codeblock}

####iOS
{: ios-next-steps}

```
cordova build ios
```
	{: codeblock}

```
cordova run ios
```
	{: codeblock}

## Ricezione di notifiche di push sui dispositivi
{: #cordova_receive}

Copia i seguenti frammenti di codice per ricevere notifiche di push sui dispositivi.

###JavaScript

Aggiungi il seguente frammento di codice JavaScript alla parte web della tua applicazione Cordova.
```
var notification = function(notification){
    // la notifica è un oggetto JSON.
alert(notification.message);
};
MFPPush.registerNotificationsCallback(notification);
```
	{: codeblock}

###Proprietà di notifica Android

La seguente selezione elenca le proprietà di notifica Android:

* message - messaggio di notifica di push
* payload - oggetto JSON che contiene un payload di notifica


###Proprietà di notifica iOS

La seguente sezione elenca le proprietà di notifica iOS:

* message - messaggio di notifica di push
* payload - oggetto JSON che contiene un payload di notifica
action-loc-key - la stringa viene utilizzata come una chiave per ottenere una stringa localizzata nella localizzazione corrente da utilizzare per il tile del pulsante appropriato, invece di `View`.
* badge - il numero da visualizzare come badge dell'icona applicazione. Se questa proprietà
                            non è presente, il badge non viene modificato. Per rimuovere il badge, imposta
                            il valore di questa proprietà su 0.
* sound - il nome di un file audio nel bundle dell'applicazione o nella cartella
                            Library/Sounds dei contenitore di dati dell'applicazione.

###Objective-C

Aggiungi i seguenti frammenti di codice Objective-C alla classe delegato della tua applicazione.

```
// Gestisci la ricezione di una notifica remota
-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler {
 [[CDVMFPPush sharedInstance] didReceiveRemoteNotification:userInfo];
}
```
	{: codeblock}


```
// Gestisci la ricezione di una notifica all'avvio
		- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    [[CDVMFPPush sharedInstance] didReceiveRemoteNotificationOnLaunch:launchOptions];
	}
```
	{: codeblock}

###Swift

Aggiungi i seguenti frammenti di codice Swift alla classe delegato della tua applicazione.
```
// Gestisci la ricezione di una notifica remota
funcapplication(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: ){
  CDVMFPPush.sharedInstance().didReceiveRemoteNotification(userInfo)
	}
```
	{: codeblock}

```
// Gestisci la ricezione di una notifica remota all'avvio
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
  CDVMFPPush.sharedInstance().didReceiveRemoteNotificationOnLaunch(launchOptions)
	}
```
	{: codeblock}

## Invio di notifiche di push di base
{: #push-send-notifications}

Dopo che hai sviluppato le tue applicazioni, puoi inviare delle notifiche di push di base.

Per inviare notifiche push di base, completa la seguente procedura:

1. Seleziona **Send Notifications** e componi un messaggio scegliendo l'opzione **Send to**. Le opzioni supportate sono **Device by Tag**, **Device Id**, **User Id**, **Android devices**, **iOS devices**, **Web Notifications** e **All Devices**.
**Nota**: quando selezioni l'opzione **All Devices**, tutti i dispositivi sottoscritti a {{site.data.keyword.mobilepushshort}} riceveranno le notifiche.
![Schermata notifiche](images/tag_notification.jpg)

2. Nel campo **Message**, componi il messaggio. Scegli di configurare le impostazioni facoltative come richiesto.
3. Fai clic su **Send**.
3. Verifica che i tuoi dispositivi abbiano ricevuto la tua notifica.

Il seguente screenshot mostra una casella di avviso che gestisce una {{site.data.keyword.mobilepushshort}} in primo piano su un dispositivo Android e iOS.

![Notifica push in primo piano su Android](images/Android_Screenshot.jpg)

![Notifica push in primo piano su iOS](images/iOS_Screenshot.jpg)

   La seguente immagine mostra {{site.data.keyword.mobilepushshort}} in background per Android.
![Notifica push in background su Android](images/background.jpg)

## Fasi successive
{: #next_steps_tags}

Dopo che hai correttamente configurato le notifiche di base, puoi configurare le notifiche
        basate sulle tag e le opzioni avanzate.

Aggiungi le funzioni del servizio {{site.data.keyword.mobilepushshort}} alla tua applicazione.
Per utilizzare le notifiche basate sulle tag, vedi [Notifiche basate sulle tag](c_tag_basednotifications.html).
Per utilizzare le opzioni di notifica avanzate, vedi [Abilitazione delle notifiche di push avanzate](t_advance_badge_sound_payload.html).
