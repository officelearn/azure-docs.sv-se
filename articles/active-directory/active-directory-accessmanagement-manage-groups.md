<egenskaper

    pageTitle="Managing groups in Azure Active Directory | Microsoft Azure"
    description="How to create and manage groups to manage Azure users using Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="curtand"/>


# Hantera grupper i Azure Active Directory

En av användarhanteringsfunktionerna i Azure Active Directory (AD Azure) är möjligheten att skapa grupper av användare. Du kan sedan använda en grupp för att tilldela licenser till en klass med användare. Du kan också använda grupper för att ge behörighet till

- resurser, till exempel objekt i katalogen,
- resurser som är externa för katalogen som SaaS-program, Azure-tjänster, SharePoint-webbplatser eller lokala resurser.

En resursägare kan dessutom ge en Azure AD-grupp åtkomst till en resurs. På så vis får medlemmarna i den aktuella gruppen åtkomst till resursen. Gruppens ägare hanterar sedan medlemskap i gruppen. I själva verket delegerar resursägaren behörighet till gruppens ägare att tilldela användare till resursen.

## Hur skapar jag en grupp?

Den här uppgiften kan utföras med hjälp av Office 365-kontoportalen, Windows Intune-kontoportalen eller den klassiska Azure-portalen, beroende på vilka tjänster som din organisation prenumererar på. Mer information om hur du hanterar Azure Active Directory med hjälp av andra portaler än Azure-portalen finns i [Administrera Azure AD-katalogen](active-directory-administer.md).

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du **Active Directory** och väljer sedan namnet på din organisations katalog.

2. Välj fliken **Grupper**.

3. Välj **Lägg till grupp**.

4. I fönstret **Lägg till grupp** anger du namnet och beskrivningen för en grupp.


## Hur lägger jag till eller tar bort enskilda användare i en säkerhetsgrupp?

**Så här lägger du till en enskild användare till en grupp**

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du **Active Directory** och väljer sedan namnet på din organisations katalog.

2. Välj fliken **Grupper**.

3. Öppna den grupp som du vill lägga till medlemmar i. Som standard visas fliken **Medlemmar** för den valda gruppen.

4. Välj **Lägg till medlemmar**.

5. På sidan **Lägg till medlemmar** väljer du namnet på den användare eller grupp som du vill lägga till som en medlem i den här gruppen och kontrollerar att namnet läggs till i rutan **Valda**.


**Så här tar du bort en användare från en grupp**

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du **Active Directory** och väljer sedan namnet på din organisations katalog.

2. Välj fliken **Grupper**.

3. Öppna den grupp som du vill ta bort medlemmar från.

4. Välj fliken **Medlemmar**, välj namnet på medlemmen som du vill ta bort från den här gruppen och klicka sedan på **Ta bort**.

6. Bekräfta att du vill ta bort medlemmen från gruppen när du uppmanas att göra det.


## Hur kan jag hantera medlemskapet för en grupp dynamiskt?

I Azure AD kan du enkelt skapa en enkel regel (en regel som bara gör en jämförelse) för att avgöra vilka användare som ska vara medlemmar i gruppen. Om en grupp exempelvis har tilldelats ett SaaS-program och du skapar en regel för att lägga till användare som har befattningen ”Säljare” så får alla användare i din Azure AD-katalog med den befattningen åtkomst till SaaS-programmet.

> [AZURE.NOTE] Du kan skapa en regel för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper. Kapslade gruppmedlemskap stöds inte för gruppbaserad tilldelning till program just nu.
>
> Dynamiskt medlemskap för grupper kräver en Azure AD Premium-licens som tilldelas till
>
> - administratören som hanterar regeln för en grupp
> - alla användare som väljs ut av regeln som medlemmar i gruppen.

**Så här aktiverar du dynamiskt medlemskap för en grupp**

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du **Active Directory** och väljer sedan namnet på din organisations katalog.

2. Välj fliken **Grupper** och öppna den grupp som du vill redigera.

3. Välj fliken **Konfigurera** och ändra **Aktivera dynamiskt medlemskap** till **Ja**.

4. Skapa en enkel regel för gruppen som ska styra hur dynamiskt medlemskap för gruppen fungerar. Kontrollera att alternativet **Lägg till användare där** är valt och välj sedan en användaregenskap i listan (till exempel avdelning, befattning osv.)

5. Välj ett villkor (inte lika med, lika med, börjar inte med, börjar med, innehåller ej, innehåller, matchar inte, matchar) och ange sedan ett värde för den valda användaregenskapen.

Mer information om hur du skapar *avancerade* regler (regler som kan innehålla flera jämförelser) för dynamiska gruppmedlemskap finns i [Använda attribut för att skapa avancerade regler](active-directory-accessmanagement-groups-with-advanced-rules.md).

## Ytterligare information

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)

* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

* [Vad är Azure Active Directory?](active-directory-whatis.md)

* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


