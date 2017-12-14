
## <a name="set-up-your-project"></a>Konfigurera ditt projekt

> Om du vill hämta det här exemplet Android Studio-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) och gå vidare till den [konfigurationssteget](#create-an-application-express) konfigurera kodexemplet innan du kör.


### <a name="create-a-new-project"></a>Skapa ett nytt projekt 
1.  Öppna Android Studio, gå till:`File` > `New` > `New Project`
2.  Namnge ditt program och klicka på`Next`
3.  Se till att välja *API 21 eller nyare (Android 5.0)* och klicka på`Next`
4.  Lämna `Empty Activity`, klickar du på `Next`, sedan`Finish`


### <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Lägg till Microsoft Authentication Library (MSAL) i projektet
1.  Gå till i Android Studio:`Gradle Scripts` > `build.gradle (Module: app)`
2.  Kopiera och klistra in följande kod under `Dependencies`:

```ruby  
compile ('com.microsoft.identity.client:msal:0.1.+') {
    exclude group: 'com.android.support', module: 'appcompat-v7'
}
compile 'com.android.volley:volley:1.0.0'
```

<!--start-collapse-->
### <a name="about-this-package"></a>Om det här paketet

Ovanstående paketet installerar Microsoft Authentication Library (MSAL). MSAL hanterar införskaffa, cachelagring och uppdatera användartoken som används för åtkomst till API: er som skyddas av Azure Active Directory v2 slutpunkt.
<!--end-collapse-->

## <a name="create-your-applications-ui"></a>Skapa ditt programs gränssnitt

1.  Öppna: `activity_main.xml` under`res` > `layout`
2.  Ändra layouten aktivitet från `android.support.constraint.ConstraintLayout` eller annan till`LinearLayout`
3.  Lägg till `android:orientation="vertical"` egenskapen `LinearLayout` nod
4.  Kopiera och klistra in följande kod i den `LinearLayout` nod, ersätter det aktuella innehållet:

```xml
<TextView
    android:text="Welcome, "
    android:textColor="#3f3f3f"
    android:textSize="50px"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dp"
    android:layout_marginTop="15dp"
    android:id="@+id/welcome"
    android:visibility="invisible"/>

<Button
    android:id="@+id/callGraph"
    android:text="Call Microsoft Graph"
    android:textColor="#FFFFFF"
    android:background="#00a1f1"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="200dp"
    android:textAllCaps="false" />

<TextView
    android:text="Getting Graph Data..."
    android:textColor="#3f3f3f"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="5dp"
    android:id="@+id/graphData"
    android:visibility="invisible"/>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="0dip"
    android:layout_weight="1"
    android:gravity="center|bottom"
    android:orientation="vertical" >

    <Button
        android:text="Sign Out"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="15dp"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:textAllCaps="false"
        android:id="@+id/clearCache"
        android:visibility="invisible" />
</LinearLayout>
```

