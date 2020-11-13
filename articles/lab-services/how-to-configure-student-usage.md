---
title: Konfigurera användnings inställningar i klass labb labb med Azure Lab Services
description: Lär dig hur du konfigurerar antalet studenter för ett labb, gör dem registrerade med labbet, styr antalet timmar som de kan använda den virtuella datorn och mer.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: d3100f1a7e67e3b0d403375de02cb3daf5fcfb31
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555727"
---
# <a name="add-and-manage-lab-users"></a>Lägg till och hantera labbanvändare

Den här artikeln beskriver hur du lägger till student användare i ett labb, registrerar dem med labbet, styr antalet ytterligare timmar som de kan använda den virtuella datorn (VM) med mera. 

## <a name="add-users-to-a-lab"></a>Lägga till användare i ett labb

I det här avsnittet lägger du till studenter i ett labb manuellt eller genom att ladda upp en CSV-fil. Gör följande:

1. I det vänstra fönstret väljer **du användare**. 

    Som standard är alternativet **begränsa åtkomst** aktiverat och om de inte finns med i listan över användare kan studenter inte registrera sig för labbet även om de har en registrerings länk. Endast listade användare kan registrera sig i labbet med hjälp av den registrerings länk som du skickar. I den här proceduren ska du lägga till användare i listan. Du kan också inaktivera **begränsa åtkomst** , vilket gör att eleverna kan registrera sig för labbet så länge de har registrerings länken. 

1. Välj **Lägg till användare** längst upp i fönstret **användare** och välj sedan **Lägg till efter e-postadress**. 

    ![Knappen Lägg till användare](./media/how-to-configure-student-usage/add-users-button.png)

1. I fönstret **Lägg till användare** anger du elevernas e-postadresser på separata rader eller på en enda rad som avgränsas med semikolon. 

    ![Lägg till användares e-postadresser](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Välj **Spara**. 

    I listan visas e-postadresser och status för de aktuella användarna, oavsett om de är registrerade i labbet eller inte. 

    ![Användarlista](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > När eleverna har registrerats i labbet visas deras namn i listan. Namnet som visas i listan skapas med hjälp av de första och sista namnen på eleverna i Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Lägg till användare genom att ladda upp en CSV-fil

Du kan också lägga till användare genom att ladda upp en CSV-fil som innehåller sina e-postadresser. 

En CSV-textfil används för att lagra kommaavgränsade tabell data (siffror och text). I stället för att lagra information i kolumn fält (till exempel i kalkyl blad) lagrar en CSV-fil information som avgränsas med kommatecken. Varje rad i en CSV-fil kommer att ha samma antal kommaavgränsade "fält". Du kan använda Excel för att enkelt skapa och redigera CSV-filer.

1. I Microsoft Excel skapar du en CSV-fil med en lista över studenters e-postadresser i en kolumn.

    ![Lista med användare i en CSV-fil](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. Välj **Lägg till användare** längst upp i fönstret **användare** och välj sedan **Ladda upp CSV**.

    ![Knappen "överför CSV"](./media/how-to-configure-student-usage/upload-csv-button.png)

1. Välj den CSV-fil som innehåller elevernas e-postadresser och välj sedan **Öppna**.

    I fönstret **Lägg till användare** visas listan med e-POSTADRESSER från CSV-filen. 

    ![Fönstret "Lägg till användare" med e-postadresser från CSV-filen](./media/how-to-configure-student-usage/add-users-window.png)

1. Välj **Spara**. 

1. I fönstret **användare** kan du se listan med tillagda studenter. 

    ![Lista över tillagda användare i fönstret "användare"](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Skicka inbjudningar till användare

Om du vill skicka en registrerings länk till nya användare kan du använda någon av följande metoder. 

Om alternativet **begränsa åtkomst** är aktiverat för labbet kan endast listade användare använda registrerings länken för att registrera sig på labbet. Det här alternativet är aktiverat som standard. 

### <a name="invite-all-users"></a>Bjud in alla användare

Den här metoden visar hur du skickar e-post med en registrerings länk och ett valfritt meddelande till alla elever i listan.

1. I fönstret **användare** väljer du **Bjud in alla**. 

    ![Knappen Bjud in alla](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. I fönstret **Skicka inbjudan per e-post** anger du ett valfritt meddelande och väljer sedan **Skicka**. 

    E-postmeddelandet innehåller automatiskt registrerings länken. Om du vill hämta och spara registrerings länken separat väljer du ellipsen ( **...** ) överst i fönstret **användare** och väljer sedan **registrerings länken**. 

    ![Fönstret "skicka registrerings länk via e-post"](./media/tutorial-setup-classroom-lab/send-email.png)

    Kolumnen **inbjudan** i listan **användare** visar status för inbjudan för varje tillagd användare. Statusen bör ändras till att **skickas** och sedan **\<date> skickas** till. 

### <a name="invite-selected-users"></a>Bjud in valda användare

Den här metoden visar hur du bara bjuder in vissa studenter och får en registrerings länk som du kan dela med andra.

1. I fönstret **användare** väljer du en student eller flera studenter i listan. 

1. I raden för den student du har valt väljer du **Kuvert** ikonen eller väljer **Bjud in** i verktygsfältet. 

    ![Bjud in valda användare](./media/how-to-configure-student-usage/invite-selected-users.png)

1. I fönstret **Skicka inbjudan per e-post** anger du ett valfritt **meddelande** och väljer sedan **Skicka**. 

    ![Skicka e-post till markerade användare](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Rutan **användare** visar status för den här åtgärden i kolumnen **inbjudan** i tabellen. E-postinbjudan innehåller en registrerings länk som eleverna kan använda för att registrera sig i labbet.

## <a name="get-the-registration-link"></a>Hämta registrerings länken

I det här avsnittet kan du hämta registrerings länken från portalen och skicka den med hjälp av ditt eget e-postprogram. 

1. I fönstret **användare** väljer du **registrerings länk**.

    ![Länk för elevregistrering](./media/how-to-configure-student-usage/registration-link-button.png)

1. I fönstret **användar registrering** väljer du **Kopiera** och sedan **slutförd**. 

    ![Fönstret "Användar registrering"](./media/how-to-configure-student-usage/registration-link.png)

    Länken kopieras till Urklipp. 
    
1. I ditt e-postprogram klistrar du in registrerings länken och skickar sedan e-postmeddelandet till en student så att studenten kan registreras för klassen. 

## <a name="view-registered-users"></a>Visa registrerade användare

1. Gå till [Azure Lab Services](https://labs.azure.com) webbplats. 
1. Välj **Logga** in och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton.
1. På sidan **Mina labb** väljer du det labb vars användning du vill spåra. 
1. I det vänstra fönstret väljer du **användare** eller panelen **användare** . 

    I fönstret **användare** visas en lista med studenter som har registrerat sig för ditt labb.  

    ![Lista över registrerade användare](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Ange kvoter för användare

Du kan ställa in en Tim kvot för varje student genom att göra följande: 

1. I fönstret **användare** väljer du **kvot per användare: \<number> timme (er)** i verktygsfältet. 
1. I fönstret **kvot per användare** anger du antalet timmar som du vill ge varje student utanför den schemalagda klass tiden och väljer sedan **Spara**.

    ![Fönstret "kvot per användare"](./media/how-to-configure-student-usage/quota-per-user.png)    

    De ändrade värdena visas nu på knappen **kvot per användare: \<number of hours>** i verktygsfältet och i listan användare, som du ser här:

    ![Kvot timmar per användare](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Den [schemalagda körningen av virtuella datorer](how-to-create-schedules.md) räknas inte av mot den kvot som tilldelas en student. Kvoten är för tiden utanför schemalagda timmar som en student tillbringar på virtuella datorer. 

## <a name="set-additional-quotas-for-specific-users"></a>Ange ytterligare kvoter för vissa användare

Du kan ange kvoter för vissa studenter utöver de vanliga kvoter som har angetts för alla användare i föregående avsnitt. Om du till exempel vill ställa in kvoten för alla studenter på 10 timmar och ange en ytterligare kvot på 5 timmar för en speciell student, får eleven 15 (10 + 5) timmar med kvot. Om du ändrar den gemensamma kvoten senare till, till exempel 15, får studenten 20 (15) timmar av kvoten. Kom ihåg att denna övergripande kvot ligger utanför den schemalagda tiden. Den tid som en student tillbringar på en virtuell labb dator under den schemalagda tiden räknas inte mot den här kvoten. 

Gör så här om du vill ange ytterligare kvoter:

1. I fönstret **användare** väljer du en elev i listan och väljer sedan **Justera kvoten** i verktygsfältet. 

    ![Knappen "justera kvot"](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. I **Justera kvoten för \<selected user or users email address>** anger du det antal ytterligare labb timmar som du vill bevilja den valda eleven eller eleverna. Välj sedan **Använd**. 

    !["Justera kvot..." terminalfönstret](./media/how-to-configure-student-usage/additional-quota.png)

    I kolumnen **användning** visas den uppdaterade kvoten för de valda eleverna. 

    ![Ny användning för användaren](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Elev konton

Om du vill lägga till studenter i ett klass rums labb använder du sina e-postkonton. Eleverna kan ha följande typer av e-post konton:

- Ett student-e-postkonto som tillhandahålls av ditt universitets Azure Active Directory-instans.
- Ett e-postkonto från Microsoft-domän, till exempel *Outlook.com* , *hotmail.com* , *MSN.com* eller *Live.com*.
- Ett e-postkonto som inte kommer från Microsoft, till exempel ett som tillhandahålls av Yahoo! eller Google. Dessa typer av konton måste dock länkas till ett Microsoft-konto.
- Ett GitHub-konto. Kontot måste vara länkat till ett Microsoft-konto.

### <a name="use-a-non-microsoft-email-account"></a>Använd ett e-postkonto som inte kommer från Microsoft

Studenter kan använda e-postkonton som inte kommer från Microsoft för att registrera sig och logga in i ett klass rums labb.  Registreringen kräver dock att du först skapar en Microsoft-konto som är länkad till e-postadressen som inte kommer från Microsoft.

Många studenter kanske redan har en Microsoft-konto som är länkad till e-postadressen som inte kommer från Microsoft. Studenter har till exempel redan en Microsoft-konto om de har använt sin e-postadress med andra produkter eller tjänster från Microsoft, t. ex. Office, Skype, OneDrive eller Windows.  

När eleverna använder registrerings länken för att logga in i ett klass rum uppmanas de att ange sina e-postadresser och lösen ord. Studenter som försöker logga in med en icke-Microsoft-konto som inte är länkad till en Microsoft-konto får följande fel meddelande: 

![Fel meddelande vid inloggning](./media/how-to-configure-student-usage/cant-find-account.png)

Här är en länk för studenter som [registrerar sig för en Microsoft-konto](http://signup.live.com).  

> [!IMPORTANT]
> När eleverna loggar in i ett klass rums labb får de inte möjlighet att skapa en Microsoft-konto. Av den anledningen rekommenderar vi att du inkluderar denna registrerings länk http://signup.live.com i e-postmeddelandet för klass rums labb registrering som du skickar till studenter som använder konton som inte kommer från Microsoft.

### <a name="use-a-github-account"></a>Använd ett GitHub-konto

Studenter kan också använda ett befintligt GitHub-konto för att registrera sig och logga in i ett klass rums labb. Om de redan har en Microsoft-konto länkat till sitt GitHub-konto kan studenter logga in och ange sitt lösen ord som visas i föregående avsnitt. 

Om de inte har länkat sitt GitHub-konto till en Microsoft-konto kan de göra följande:

1. Välj länken **inloggnings alternativ** , som du ser här:

    ![Länken "inloggnings alternativ"](./media/how-to-configure-student-usage/signin-options.png)

1. I fönstret **inloggnings alternativ** väljer du **Logga in med GitHub**.

    ![Länken "logga in med GitHub"](./media/how-to-configure-student-usage/signin-github.png)

    I meddelandet skapar eleverna en Microsoft-konto som är länkad till sitt GitHub-konto. Länkningen sker automatiskt när de väljer **Nästa**. De är sedan omedelbart inloggade och anslutna till klass rummets labb miljö.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exportera en lista med användare till en CSV-fil

1. Gå till fönstret **användare** .
1. Välj ellipsen ( **...** ) i verktygsfältet och välj sedan **exportera CSV**. 

    ![Knappen "exportera CSV"](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- För administratörer: [skapa och hantera labb konton](how-to-manage-lab-accounts.md)
- För labb ägare: [skapa och hantera labb](how-to-manage-classroom-labs.md) och [Konfigurera och publicera mallar](how-to-create-manage-template.md)
- För labb användare: [komma åt klass rum labb](how-to-use-classroom-lab.md)
