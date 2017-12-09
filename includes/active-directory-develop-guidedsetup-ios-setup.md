
## <a name="setting-up-your-ios-application"></a>Konfigurera iOS-program

Det här avsnittet innehåller stegvisa instruktioner för hur du skapar ett nytt projekt för att demonstrera hur du integrerar en iOS-App (Swift) med *logga In med Microsoft* så att den kan fråga webb-API: er som kräver en token.

> Om du vill hämta det här exemplet XCode-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) och gå vidare till den [konfigurationssteget](#create-an-application-express) konfigurera kodexemplet innan du kör.


## <a name="install-carthage-to-download-and-build-msal"></a>Installera Carthage om du vill hämta och skapa MSAL
Carthage Pakethanteraren används i förhandsversionen av MSAL – den kan integreras med XCode samtidigt möjligheten för Microsoft för att göra ändringar i biblioteket.

- Hämta och installera den senaste versionen av Carthage [här](https://github.com/Carthage/Carthage/releases "Carthage nedladdnings-URL")

## <a name="creating-your-application"></a>Skapar ditt program

1.  Öppna Xcode och välj`Create a new Xcode project`
2.  Välj `iOS`  >  `Single view Application` och på *nästa*
3.  Ge ett produktnamn och klicka på *nästa*
4.  Välj en mapp för att skapa din app och klicka på *skapa*

## <a name="build-the-msal-framework"></a>Skapa MSAL Framework

Följ instruktionerna nedan när du hämtar och skapa sedan den senaste versionen av MSAL bibliotek med Carthage:

1.  Öppna terminalen bash och gå till appens rotmapp
2.  Kopiera den nedan och klistra in i bash terminalen för att skapa en 'Cartfile'-fil:

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Kopiera och klistra in den nedan. Detta kommando hämtar beroenden till en Carthage/utcheckningar mapp och sedan skapar MSAL-biblioteket:
</li>
</ol>

```bash
carthage update
```

> Processen ovan används för att hämta och skapa Microsoft Authentication Library (MSAL). MSAL hanterar införskaffa, cachelagring och uppdatera användartoken som används för åtkomst till API: er som skyddas av Azure Active Directory-v2.

## <a name="add-the-msal-framework-to-your-application"></a>Lägg till MSAL framework i ditt program
1.  I Xcode öppnar den `General` fliken
2.  Gå till den `Linked Frameworks and Libraries` avsnittet och klicka på`+`
3.  Välj `Add other…`
4.  Välj: `Carthage`  >  `Build`  >  `iOS`  >  `MSAL.framework` och på *öppna*. Du bör se `MSAL.framework` läggs till i listan.
5.  Gå till `Build Phases` och på `+` ikon, Välj`New Run Script Phase`
6.  Lägg till följande innehåll till den *skript området*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Lägg till följande till <code>Input Files</code> genom att klicka på <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Skapa programmets användargränssnitt
En Main.storyboard-fil ska skapas automatiskt som en del av projektmallen för. Följ anvisningarna nedan för att skapa UI-app:

1.  CTRL + klicka `Main.storyboard` öppna snabbmenyn och klicka sedan på:`Open As` > `Source Code`
2.  Ersätt den `<scenes>` nod med koden nedan:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
