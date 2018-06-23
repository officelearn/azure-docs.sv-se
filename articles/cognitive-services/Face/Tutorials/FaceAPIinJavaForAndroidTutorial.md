---
title: Står inför API Java för Android självstudiekursen | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Skapa en enkel Android-app som använder kognitiva Services Ansikts-API för att identifiera och RAM mänskliga står i en bild.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355188"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Komma igång med Ansikts-API i Java Android genomgång

I kursen får du lära dig att skapa och utveckla en enkel Android-program som anropar Ansikts-API för att identifiera mänsklig ytor i en bild. Programmet visas resultatet av framing ytor som upptäcks.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Förberedelse

Den här självstudiekursen måste följande krav:

- Android Studio och SDK är installerat
- Android-enhet (valfritt för testning).

## <a name="step1"></a>Steg 1: Prenumerera Ansikts-API: t och hämta nyckel för din prenumeration

Innan du använder alla Ansikts-API: er, måste du registrera dig att prenumerera på framsidan API i portalen kognitiva Microsoft-tjänster. Se [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både primära och sekundära nyckeln kan användas i den här självstudiekursen.

## <a name="step2"></a>Steg 2: Skapa application framework

I det här steget skapar du ett projekt för Android-program för att implementera grundläggande Användargränssnittet för att hämta och visa en bild. Bara följa anvisningarna nedan: 

1. Öppna Android Studio.
2. Från menyn Arkiv klickar du på **nytt projekt...**
3. Ge programmet namnet **MyFirstApp**, och klicka sedan på Nästa. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Välj målplattformen efter behov och klicka sedan på Nästa. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Välj **grundläggande aktiviteten** och klicka sedan på Nästa.
6. Namnge aktiviteten enligt följande och klicka sedan på Slutför. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Öppna **activity_main.xml**, bör du se redigeraren Layout av denna aktivitet.
8. Visa källa textfil och sedan redigera aktiviteten layouten på följande sätt:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Öppna **MainActivity.java** och infoga följande importdirektiv i början av filen:

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
    ```
      
    För det andra ändra klassen:  
    
    ```java
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
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Din app kan nu bläddra efter ett foto från galleriet och visas i fönstret liknar bilden nedan:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Steg 3: Konfigurera klientbiblioteket Ansikts-API

Ansikts-API: et är ett moln API som du kan anropa med hjälp av HTTPS-begäranden. Ett enklare sätt i Ansikts-API i .NET-plattformen program finns också en klientbiblioteket kapsla in webb-begär. I det här exemplet använder vi klientbiblioteket för att förenkla vårt arbete. 

Följ anvisningarna nedan för att konfigurera klientbiblioteket: 

1. Leta reda på den översta **build.gradle** -filen för ditt projekt från panelen projekt som visas i exemplet. Observera att det finns flera andra **build.gradle** filer i ditt projektträdet, och du behöver öppna den översta **build.gradle** filen först.
2. Lägg till **mavenCentral()** till dina projekt databaser. Du kan också använda jcenter() som är standard lagringsplatsen för Android Studio eftersom jcenter() är en supermängd mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Öppna den **build.gradle** filen i projektet ”app”.
4. Lägg till ett beroende för våra klientbiblioteket som lagras i den centrala databasen Maven:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Öppna **MainActivity.java** i ”app” projektet och infoga följande importera direktiven: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Infoga följande kod i klassen:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   Ersätt den första parametern ovan med API-slutpunkt som har tilldelats din nyckel i steg 1. Exempel:
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Ersätt den andra parametern med nyckeln prenumeration som du hämtade i steg 1.
   
6. Öppna filen **AndroidManifest.xml** i projektet ”app”. Infoga följande element som underordnad till den **manifest** element:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Du är nu redo att anropa Ansikts-API från ditt program. 

## <a name="step4"></a>Steg 4: Överför bilder att identifiera ytor

Det enklaste sättet att identifiera ytor är genom att anropa den [står inför – identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API genom att överföra image-filen direkt. När du använder klientbiblioteket, kan du göra det med hjälp av asynkrona metoden **DetectAsync** av den **FaceServiceClient** klass. Varje returnerade yta innehåller en rektangel för att ange dess plats i kombination med en serie med valfritt ansikte attribut. I det här exemplet behöver vi bara hämta ansikts-platsen. Här vi behöver infoga en metod i den **MainActivity** klass för framsidan identifiering: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
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
```

## <a name="step5"></a>Steg 5: Markera står i avbildningen

I det sista steget vi kombinera alla ovanstående steg tillsammans och markera de identifierade personerna bakom ramar i avbildningen. Öppna först **MainActivity.java** och infoga en hjälpmetod för att Rita rektanglar: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
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

Nu slutföra TODO-delar i den **detectAndFrame** metod för att ram ytor och rapportera status.

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
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Slutligen lägger du till ett anrop till den **detectAndFrame** metod från den **onActivityResult** metod, enligt nedan. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Kör det här programmet och bläddra efter en avbildning som innehåller ett ansikte. Vänta några sekunder att molnet API för att svara. Efter att får du ett resultat liknar bilden nedan: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Sammanfattning

I den här självstudiekursen du lärt dig det grundläggande sättet att med hjälp av Ansikts-API och skapat ett program för att visa framsidan markeringar i bilder. Mer information om Ansikts-API finns anvisningar och [API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Relaterade självstudier

- [Komma igång med framsidan API i CSharp självstudiekursen](FaceAPIinCSharpTutorial.md)
- [Komma igång med framsidan API i Python självstudiekursen](FaceAPIinPythonTutorial.md)
