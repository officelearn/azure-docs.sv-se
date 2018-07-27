---
title: Självstudie om Ansikts-API Java för Android | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: I den här självstudien skapar du en enkel Android-app som använder Ansiktstjänsten för Cognitive Services för att identifiera och rama in ansikten i en bild.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: ad7b85b378db9e9687b5f8081bc9832e91e9ee5e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125644"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Självstudie: Skapa en Android-app för att upptäcka och rama in ansikten i en bild

I den här självstudien skapar du ett enkelt Android-program som använder Ansiktstjänstens Java-klassbibliotek för att identifiera människors ansikten i en bild. Programmet visar en vald bild med varje identifierat ansikte inramat i en rektangel. Den fullständiga exempelkoden finns på GitHub på [Identifiera och rama in ansikten i en bild på Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample).

![Android-skärmbild av ett foto med ansikten inramade i en röd rektangel](../Images/android_getstarted2.1.PNG)

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> - Skapa ett Android-program
> - Installera Ansiktstjänstens klientbibliotek
> - Använda klientbiblioteket för att identifiera ansikten i en bild
> - Rita en ram runt varje identifierat ansikte

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du behöver en prenumerationsnyckel för att köra exemplet. Du kan hämta nycklar för kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- [Android Studio](https://developer.android.com/studio/) med minst SDK 22 (krävs av klientbiblioteket för Ansiktsigenkänning).
- Klientbiblioteket för Ansiktsigenkänning [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) från Maven. Du behöver inte ladda ned paketet. Installationsinstruktioner finns nedan.

## <a name="create-the-project"></a>Skapa projektet

Skapa ditt Android-programprojekt genom att följa dessa steg:

1. Öppna Android Studio. I den här självstudiekursen används Android Studio 3.1.
1. Välj **Start a new Android Studio project** (Starta ett nytt Android Studio-projekt).
1. På skärmen **Create Android Project** (Skapa Android-projekt) ändrar du standardfälten vid behov och klickar sedan på **Next** (Nästa).
1. På skärmen **Target Android Devices** (Android-målenheter) använder du listrutan för att välja **API 22** eller senare och klickar sedan på **Next** (Nästa).
1. Välj **Empty Activity** (Tom aktivitet) och klicka sedan på **Next** (Nästa).
1. Avmarkera **Backwards Compatibility** (Bakåtkompatibilitet) och klicka sedan på **Finish** (Slutför).

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Skapa användargränssnittet för att välja och visa bilden

Öppna *activity_main.xml*; du bör se layoutredigeraren. Välj fliken **Text** och ersätt innehållet med följande kod.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Öppna *MainActivity.java* och ersätt allt förutom det första `package`-uttrycket med följande kod.

Koden anger en händelsehanterare på `Button` som startar en ny aktivitet så att användaren kan välja en bild. När den har valts visas bilden `ImageView`.

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Nu kan din app bläddra efter ett foto och visa det i fönstret, ungefär som på bilden nedan.

![Android-skärmbild av ett foto med ansikten](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Konfigurera klientbiblioteket för Ansiktsigenkänning

Ansikts-API är ett moln-API som du kan anropa med hjälp av HTTPS-begäranden. Den här självstudien använder klientbiblioteket för Ansiktsigenkänning, som kapslar in dessa webbegäranden, för att förenkla arbetet.

I fönsterrutan **Project** (Projekt) använder du listrutan för att välja **Android**. Expandera **Gradle Scripts** (Gradle-skript) och öppna sedan *build.gradle (Module: app)*.

Lägg till ett beroende för klientbiblioteket för Ansiktsigenkänning, `com.microsoft.projectoxford:face:1.4.3`, enligt skärmbilden nedan, och klicka sedan på **Sync Now** (Synkronisera nu).

![Android Studio-skärmbild på appens build.gradle-fil](../Images/face-tut-java-gradle.png)

Öppna **MainActivity.java** och lägg till följande importdirektiv:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Lägg till koden för klientbiblioteket för Ansiktsigenkänning

Infoga följande kod i `MainActivity`-klassen ovanför `onCreate`-metoden:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Ersätt `<API endpoint>` med den API-slutpunkt som har tilldelats till din nyckel. Nycklar för kostnadsfri utvärderingsprenumeration genereras i regionen **westcentralus** (USA, västra centrala). Om du använder en nyckel för kostnadsfri utvärderingsprenumeration blir uttrycket därmed:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Ersätt `<Subscription Key>` med din prenumerationsnyckel. Exempel:

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

I fönsterrutan **Project** (Projekt) expanderar du **app** och sedan **manifests** (manifest) och öppnar *AndroidManifest.xml*.

Infoga följande element som ett direkt underordnat element till `manifest`-elementet:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Skapa projektet för att söka efter fel. Nu är du redo att anropa Ansiktstjänsten.

## <a name="upload-an-image-to-detect-faces"></a>Ladda upp en bild för att identifiera ansikten

Det enklaste sättet att identifiera ansikten är att anropa metoden `FaceServiceClient.detect`. Den här metoden omsluter [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-API-metoden och returnerar en matris med `Face`.

Varje returnerat `Face` innehåller en rektangel för att ange dess plats samt en serie med valfria ansiktsattribut. I det här exemplet krävs endast ansiktsplatser.

Om ett fel inträffar visar en `AlertDialog` den underliggande orsaken.

Infoga följande metoder i klassen `MainActivity`.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Rama in ansikten i bilden

Infoga följande hjälpmetod i klassen `MainActivity`. Den här metoden ritar en rektangel runt varje identifierat ansikte.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

Slutför `AsyncTask`-metoderna enligt `TODO`-kommentarerna i metoden `detectAndFrame`. Om åtgärden lyckades visas den valda bilden med inramade ansikten i `ImageView`.

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

I metoden `onActivityResult` avkommenterar du anropet till metoden `detectAndFrame` enligt nedan.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Kör appen

Kör programmet och bläddra efter en bild med ett ansikte. Vänta några sekunder på att Ansiktstjänsten ska svara. Därefter får du ett resultat som liknar bilden nedan:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Sammanfattning

I den här självstudien har du lärt dig den grundläggande processen för att använda Ansiktstjänsten och skapat ett program för att visa inramade ansikten i en bild.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du identifierar och använder ansiktsattribut.

> [!div class="nextstepaction"]
> [Så identifierar du ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Utforska de Ansikts-API:er som används för att identifiera ansikten och deras attribut, till exempel posering, kön, ålder, huvudposering, ansiktsbehåring och glasögon.

> [!div class="nextstepaction"]
> [Ansikts-API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).