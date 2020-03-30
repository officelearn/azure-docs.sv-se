---
title: Konfigurera användningsinställningar i klassrumslabb i Azure Lab Services
description: Lär dig hur du konfigurerar antalet deltagare för ett labb, får dem registrerade med labbet, styr antalet timmar de kan använda den virtuella datorn med mera.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159463"
---
# <a name="add-and-manage-lab-users"></a>Lägg till och hantera labbanvändare

I den här artikeln beskrivs hur du lägger till deltagare i ett labb, registrerar dem med labbet, styr antalet ytterligare timmar som de kan använda den virtuella datorn (VM) med mera. 

## <a name="add-users-to-a-lab"></a>Lägga till användare i ett labb

I det här avsnittet lägger du till deltagare i ett labb manuellt eller genom att ladda upp en CSV-fil. Gör följande:

1. Välj **Användare**i den vänstra rutan . 

    Som standard är alternativet **Begränsa åtkomst** aktiverat och, om de inte finns med i listan över användare, kan deltagarna inte registrera sig i labbet även om de har en registreringslänk. Endast listade användare kan registrera sig hos labbet med hjälp av registreringslänken du skickar. I den här proceduren ska du lägga till användare i listan. Alternativt kan du inaktivera **Begränsa åtkomst**, vilket gör att deltagarna kan registrera sig i labbet så länge de har registreringslänken. 

1. Högst upp i fönstret **Användare** väljer du **Lägg till användare**och väljer sedan Lägg till via **e-postadress**. 

    ![Knappen "Lägg till användare"](../media/how-to-configure-student-usage/add-users-button.png)

1. I fönstret **Lägg till användare** anger du deltagarnas e-postadresser på separata rader eller på en enda rad avgränsad med semikolon. 

    ![Lägga till användarnas e-postadresser](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Välj **Spara**. 

    Listan visar de aktuella användarnas e-postadresser och status, oavsett om de är registrerade i labbet eller inte. 

    ![Användarlista](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > När eleverna har registrerats i labbet visar listan deras namn. Namnet som visas i listan skapas med hjälp av för- och efternamnen för deltagarna i Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Lägga till användare genom att ladda upp en CSV-fil

Du kan också lägga till användare genom att ladda upp en CSV-fil som innehåller deras e-postadresser.

1. Skapa en CSV-fil i Microsoft Excel som visar elevernas e-postadresser i en kolumn.

    ![Lista över användare i en CSV-fil](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. Högst upp i fönstret **Användare** väljer du **Lägg till användare**och väljer sedan Ladda upp **CSV**.

    ![Knappen "Ladda upp CSV"](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Markera csv-filen som innehåller deltagarnas e-postadresser och välj sedan **Öppna**.

    I fönstret **Lägg till användare** visas e-postadresslistan från CSV-filen. 

    ![Fönstret "Lägg till användare" med e-postadresser från CSV-filen](../media/how-to-configure-student-usage/add-users-window.png)

1. Välj **Spara**. 

1. I fönstret **Användare** visar du listan över tillagda deltagare. 

    ![Lista över tillagda användare i fönstret "Användare"](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Skicka inbjudningar till användare

Om du vill skicka en registreringslänk till nya användare använder du någon av följande metoder. 

Om alternativet **Begränsa åtkomst** är aktiverat för labbet kan endast listade användare använda registreringslänken för att registrera sig i labbet. Det här alternativet är aktiverat som standard. 

### <a name="invite-all-users"></a>Bjud in alla användare

Den här metoden visar hur du skickar e-post med en registreringslänk och ett valfritt meddelande till alla listade deltagare.

1. Välj Bjud in **alla**i fönstret **Användare** . 

    ![Knappen "Bjud in alla"](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. I fönstret **Skicka inbjudan via e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. 

    E-postmeddelandet innehåller automatiskt registreringslänken. Om du vill hämta och spara registreringslänken separat väljer du ellipsen (**...**) högst upp i **fönstret Användare** och väljer sedan **Registreringslänken**. 

    ![Fönstret "Skicka registreringslänk via e-post"](../media/tutorial-setup-classroom-lab/send-email.png)

    I kolumnen **Inbjudan** i listan **Användare** visas inbjudningsstatus för varje tillagd användare. Statusen ska ändras till **Skicka** och sedan skickas **på \<datum>**. 

### <a name="invite-selected-users"></a>Bjud in valda användare

Den här metoden visar hur du bara bjuder in vissa deltagare och får en registreringslänk som du kan dela med andra.

1. Välj en deltagare eller flera deltagare i listan i fönstret **Användare.** 

1. På raden för den deltagare som du har markerat markerar du **kuvertikonen** eller väljer **Bjud in**i verktygsfältet . 

    ![Bjud in valda användare](../media/how-to-configure-student-usage/invite-selected-users.png)

1. I fönstret **Skicka inbjudan via e-post** anger du ett valfritt **meddelande**och väljer sedan **Skicka**. 

    ![Skicka e-post till valda användare](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    I fönstret **Användare** visas status för den här åtgärden i kolumnen **Inbjudan i** tabellen. Inbjudningsmeddelandet innehåller registreringslänken som deltagarna kan använda för att registrera sig i labbet.

## <a name="get-the-registration-link"></a>Hämta registreringslänken

I det här avsnittet kan du få registreringslänken från portalen och skicka den med hjälp av ditt eget e-postprogram. 

1. Välj **Registreringslänk**i fönstret **Användare** .

    ![Länk för elevregistrering](../media/how-to-configure-student-usage/registration-link-button.png)

1. Välj **Kopiera**i fönstret **Användarregistrering** och välj sedan **Klar**. 

    ![Fönstret "Användarregistrering"](../media/how-to-configure-student-usage/registration-link.png)

    Länken kopieras till Urklipp. 
    
1. I din e-postansökan klistrar du in registreringslänken och skickar sedan e-postmeddelandet till en elev så att eleven kan registrera sig för klassen. 

## <a name="view-registered-users"></a>Visa registrerade användare

1. Gå till [webbplatsen för Azure Lab Services.](https://labs.azure.com) 
1. Välj **Logga in**och ange sedan dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton.
1. På sidan **Mina labb** väljer du det labb vars användning du vill spåra. 
1. Välj **Användare**i den vänstra rutan eller välj panelen **Användare.** 

    I fönstret **Användare** visas en lista över deltagare som har registrerat sig i labbet.  

    ![Lista över registrerade användare](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Ange kvoter för användare

Du kan ange en timkvot för varje deltagare genom att göra följande: 

1. I fönstret **Användare** väljer du **Kvot per användare: \<nummer> timmar** i verktygsfältet. 
1. I fönstret **Kvot per användare** anger du hur många timmar du vill ge varje deltagare utanför den schemalagda klasstiden och väljer sedan **Spara**.

    ![Fönstret "Kvot per användare"](../media/how-to-configure-student-usage/quota-per-user.png)    

    De ändrade värdena visas nu på knappen **Kvot per \<användare: antal timmar>** i verktygsfältet och i användarlistan, som visas här:

    ![Kvottimmar per användare](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Den [schemalagda körtiden för virtuella datorer](how-to-create-schedules.md) räknas inte mot den kvot som tilldelats en deltagare. Kvoten är för tiden utanför schemalagda timmar som en deltagare tillbringar på virtuella datorer. 

## <a name="set-additional-quotas-for-specific-users"></a>Ange ytterligare kvoter för specifika användare

Du kan ange kvoter för vissa deltagare utöver de gemensamma kvoter som har angetts för alla användare i föregående avsnitt. Om du till exempel som lärare anger kvoten för alla deltagare till 10 timmar och anger en extra kvot på 5 timmar för en viss deltagare, får deltagaren 15 (10 + 5) kvottimmar. Om du ändrar den gemensamma kvoten senare till, säg, 15, får studenten 20 (15 + 5) kvottimmar. Kom ihåg att den här totala kvoten ligger utanför den schemalagda tiden. Den tid som en deltagare spenderar på en virtuell labbdator under den schemalagda tiden räknas inte mot den här kvoten. 

Så här anger du ytterligare kvoter:

1. Välj en deltagare i listan i fönstret **Användare** och välj sedan **Justera kvot** i verktygsfältet. 

    ![Knappen "Justera kvot"](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. I **e-postadressen Justera \<för vald användare eller användare>** anger du antalet ytterligare labbtimmar som du vill bevilja den eller de valda deltagarna och väljer sedan **Använd**. 

    !["Justera kvoten ..." Fönstret](../media/how-to-configure-student-usage/additional-quota.png)

    I kolumnen **Användning** visas den uppdaterade kvoten för de valda deltagarna. 

    ![Ny användning för användaren](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Studentkonton

Om du vill lägga till elever i ett klassrumslabb använder du deras e-postkonton. Deltagarna kan ha följande typer av e-postkonton:

- Ett student-e-postkonto som tillhandahålls av universitetets Azure Active Directory-instans för Office 365. 
- Ett e-postkonto för Microsoft-domän, till exempel *outlook.com*, *hotmail.com*, *msn.com*eller *live.com*.
- Ett e-postkonto som inte kommer från Microsoft, till exempel ett som tillhandahålls av Yahoo! eller Google. Dessa typer av konton måste dock vara kopplade till ett Microsoft-konto.
- Ett GitHub-konto. Det här kontot måste vara länkat till ett Microsoft-konto.

### <a name="use-a-non-microsoft-email-account"></a>Använda ett e-postkonto som inte kommer från Microsoft
Deltagare kan använda e-postkonton som inte kommer från Microsoft för att registrera sig och logga in på ett klassrumslabb.  Registreringen kräver dock att de först skapar ett Microsoft-konto som är kopplat till deras e-postadress som inte kommer från Microsoft.

Många studenter kanske redan har ett Microsoft-konto som är kopplat till deras e-postadress som inte kommer från Microsoft. Eleverna har till exempel redan ett Microsoft-konto om de har använt sin e-postadress med andra Produkter eller tjänster från Microsoft, till exempel Office, Skype, OneDrive eller Windows.  

När eleverna använder registreringslänken för att logga in i ett klassrum uppmanas de att ange sin e-postadress och sitt lösenord. Deltagare som försöker logga in med ett konto som inte är kopplat till ett Microsoft-konto får följande felmeddelande: 

![Felmeddelande vid inloggning](../media/how-to-configure-student-usage/cant-find-account.png)

Här är en länk för studenter att [registrera dig för ett Microsoft-konto](http://signup.live.com).  

> [!IMPORTANT]
> När eleverna loggar in på ett klassrumslabb får de inte möjlighet att skapa ett Microsoft-konto. Därför rekommenderar vi att du inkluderar den här http://signup.live.comregistreringslänken i e-postmeddelandet med registrering i klassrummet som du skickar till elever som använder konton som inte är Microsoft-konton.

### <a name="use-a-github-account"></a>Använda ett GitHub-konto
Deltagarna kan också använda ett befintligt GitHub-konto för att registrera sig och logga in på ett klassrumslabb. Om de redan har ett Microsoft-konto kopplat till sitt GitHub-konto kan deltagarna logga in och ange sitt lösenord enligt föregående avsnitt. 

Om de ännu inte har länkat sitt GitHub-konto till ett Microsoft-konto kan de göra följande:

1. Välj länken **Inloggningsalternativ,** som du ser här:

    ![Länken "Inloggningsalternativ"](../media/how-to-configure-student-usage/signin-options.png)

1. I fönstret **Inloggningsalternativ** väljer du **Logga in med GitHub**.

    ![Länken "Logga in med GitHub"](../media/how-to-configure-student-usage/signin-github.png)

    Vid prompten skapar eleverna sedan ett Microsoft-konto som är kopplat till deras GitHub-konto. Länkningen sker automatiskt när de väljer **Nästa**. De är sedan omedelbart inloggade och anslutna till klassrummet labbet.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exportera en lista över användare till en CSV-fil

1. Gå till fönstret **Användare.**
1. Markera ellipsen (**...**) i verktygsfältet och välj sedan **Exportera CSV**. 

    ![Knappen "Exportera CSV"](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- För administratörer: [Skapa och hantera labbkonton](how-to-manage-lab-accounts.md)
- För labbägare: [Skapa och hantera labb](how-to-manage-classroom-labs.md) och Konfigurera och publicera [mallar](how-to-create-manage-template.md)
- För labbanvändare: [Få tillgång till klassrumslabb](how-to-use-classroom-lab.md)
