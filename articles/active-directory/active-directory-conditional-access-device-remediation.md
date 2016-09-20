<properties
    pageTitle="Felsökning: Du kan inte ta dig dit härifrån | Microsoft Azure"
    description="Det här avsnittet hjälper dig att identifiera steg som du kan utföra för att få åtkomst till ett program."
    services="active-directory"
    keywords="enhetsbaserad villkorlig åtkomst, enhetsregistrering, aktivera enhetsregistrering, enhetsregistrering och MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# Felsökning: Du kan inte ta dig dit härifrån

En ”nekad åtkomst”-sida visas när du försökte ansluta till ett program, t.ex. SharePoint Online.  
Vad gör du?

Den här guiden hjälper dig att identifiera de steg som du kan följa för att få åtkomst till programmet.



Vilken enhetsplattform körs enheten på?
Svaret på den här frågan avgör vilket avsnitt som du ska läsa i den här artikeln:


-   Windows-enhet
-   iOS-enhet (iPhone eller iPad)
-   Android-enhet

## Åtkomst från en Windows-enhet

Om din enhet kör Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2 väljer du relevant orsak genom att identifiera sidan som du kom till när du försökte komma åt programmet.

### Enheten har inte registrerats

Om enheten inte är registrerad med Azure Active Directory (AD Azure) och programmet är skyddat med en enhetsbaserad princip kanske en sida med följande innehåll visas:

![Meddelandet ”Du kan inte ta dig dit härifrån” för enheter som inte har registrerats](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")



Om enheten är domänansluten till Active Directory i din organisation kan du prova följande:

1.  Kontrollera att du har loggat in i Windows med ditt arbetskonto (Active Directory-konto).
2.  Anslut till företagsnätverket via VPN eller DirectAccess.
3.  När du har anslutit låser du Windows-sessionen med hjälp av Windows-tangenten + L-tangenten.
4.  Lås upp Windows-sessionen genom att ange autentiseringsuppgifterna för ditt arbetskonto.
5.  Vänta en stund och prova sedan att ansluta till programmet igen.
6.  Om samma sida visas kontaktar du din administratör, klickar på länken **Mer information** och anger informationen.

Om enheten inte är domänansluten och kör Windows 10 kan du välja mellan två alternativ:

- Kör Azure AD Join.
- Lägg till ditt arbets- eller skolkonto till Windows.

Information om skillnaderna mellan de två alternativen finns i [Använda Windows 10-enheter på arbetsplatsen](active-directory-azureadjoin-windows10-devices.md).

Om du vill köra Azure AD Join gör du följande (inte tillgängligt för Windows Phone):

**Windows 10 Anniversary Update**

1.  Öppna appen **Inställningar**.
2.  Klicka på **Konton** > **Access work or school** (Åtkomst till arbete eller skola).
3.  Klicka på **Anslut**.
4.  Klicka på **Join this device to Azure AD** (Anslut den här enheten till Azure AD) längst ned på sidan.
5.  Autentisera dig i din organisation, gå igenom multifaktorautentiseringen om det behövs och följ sedan stegen tills processen är klar.
6.  Logga ut och logga sedan in igen med ditt arbetskonto.
7.  Prova att öppna programmet igen.




**Windows 10 November 2015 Update**


1.  Öppna appen **Inställningar**.
2.  Klicka på **System** > **Om**.
3.  Klicka på **Anslut till Azure AD**.
4.  Autentisera dig i din organisation, gå igenom multifaktorautentiseringen om det behövs och följ sedan stegen tills processen är klar.
5.  Logga ut och logga sedan in igen med ditt arbetskonto (Azure AD-konto).
6.  Prova att öppna programmet igen.

Lägg till ditt arbets- eller skolkonto genom att göra följande:

**Windows 10 Anniversary Update**

1.  Öppna appen **Inställningar**.
2.  Klicka på **Konton** > **Access work or school** (Åtkomst till arbete eller skola).
3.  Klicka på **Anslut**.
4.  Autentisera dig i din organisation, gå igenom multifaktorautentiseringen om det behövs och följ sedan stegen tills processen är klar.
5.  Prova att öppna programmet igen.


**Windows 10 November 2015 Update**

1.  Öppna appen **Inställningar**.
2.  Klicka på **Konton** > **Dina konton**.
3.  Klicka på **Arbets- eller skolkonto**.
4.  Autentisera dig i din organisation, gå igenom multifaktorautentiseringen om det behövs och följ sedan stegen tills processen är klar.
5.  Prova att öppna programmet igen.

Om enheten inte är domänansluten och kör Windows 8.1 kan du använda Workplace Join och registrera enheten i Microsoft Intune genom att göra följande:

1.  Öppna **Datorinställningar**.
2.  Klicka på **Nätverk** > **Arbetsplats**.
3.  Klicka på **Anslut**.
4.  Autentisera dig i din organisation, gå igenom multifaktorautentiseringen om det behövs och följ sedan stegen tills processen är klar.
5.  Klicka på **Aktivera**.
6.  Vänta tills processen är klar.
7.  Prova att öppna programmet igen.


## Webbläsaren stöds inte

Om du öppnar programmet från följande webbläsare visas en sida liknande den som visades tidigare:

- Chrome, Firefox eller en annan webbläsare som inte är Microsoft Edge eller Microsoft Internet Explorer i Windows 10 eller Windows Server 2016.
- Firefox i Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 eller Windows Server 2008 R2.

![Meddelandet ”Du kan inte ta dig dit härifrån” för webbläsare som inte stöds](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")


Det enda du kan göra är att använda en webbläsare som stöds av programmet för din enhetsplattform.

## Åtkomst från en iOS-enhet
Kom tillbaka snart för anvisningar för iPhone- och iPad-enheter.

## Åtkomst från en Android-enhet
Kom tillbaka snart för anvisningar för Android-telefoner och Android-surfplattor.

## Nästa steg

[Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access.md)



<!--HONumber=sep16_HO1-->


