---
title: Så här skapar du ett kommersiellt marknads plats konto i Partner Center
description: Lär dig hur du skapar ett kommersiellt marknads plats konto i Partner Center.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 07/05/2019
ms.openlocfilehash: 24ed03cde967f0fef7abdc4c2df1f8e5d6bdb6ab
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327117"
---
# <a name="create-a-commercial-marketplace-account-in-partner-center"></a>Skapa ett kommersiellt marknads plats konto i Partner Center

Om du vill publicera dina erbjudanden till [Azure Marketplace](https://azuremarketplace.microsoft.com/) eller [AppSource](https://appsource.microsoft.com/)måste du skapa ett konto i det kommersiella Marketplace-programmet i Partner Center.

## <a name="create-a-partner-center-account"></a>Skapa ett partner Center-konto

I den här artikeln får du lära dig hur du skapar ett partner Center-konto, inklusive hur du: 

- [Registrera dig med hjälp av sidan för registrering av Partner Center](#to-create-a-commercial-marketplace-account-in-partner-center)
- [Logga in med ett arbets konto](#sign-in-with-a-work-account)
- [Godkänn de allmänna villkoren](#agree-to-terms-and-conditions) 
- [Ange din utgivar profil](#provide-your-publisher-profile)

>[!Important]
>Om du har ett konto i [Cloud Partner Portal (cpp)](https://cloudpartner.azure.com) som har flyttats till Partner Center behöver du inte skapa ett nytt konto. Mer information finns i [utgivare som flyttar från cpp](#publishers-moving-from-cpp) . 

### <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att du har följande för att skapa ett konto på Partner Center:

- Myndighet för att signera juridiska avtal på ditt företags vägnar.
- Ditt företags juridiska företags namn, adress och primär kontakt (detta kan vara du).

Vi ska kontrol lera den här informationen när kontot skapas.

### <a name="to-create-a-commercial-marketplace-account-in-partner-center"></a>Skapa ett kommersiellt marknads plats konto i Partner Center

Läs informationen på sidan [**Välkommen till Microsoft Partner Center**](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) -registrering och registrera dig för ett konto.

#### <a name="sign-in-with-a-work-account"></a>Logga in med ett arbets konto

Så att du kan koppla företagets jobb för e-postkontot till ditt nya partner Center-konto. Genom att associera dessa konton kan företagets anställda logga in på Partner Center med sitt arbets konto användar namn och lösen ord.

>[!Note]
>Om du vill kontrol lera om ditt företag redan har ett arbets konto, hur du skapar ett nytt arbets konto eller hur du konfigurerar flera arbets konton som ska användas med partner Center, kan du gå till [ditt företags arbets konto och partner Center](./company-work-accounts.md). 

#### <a name="agree-to-terms-and-conditions"></a>Godkänn de allmänna villkoren

Du måste acceptera två uppsättningar med villkor, inklusive [prenumerationsavtal för Microsoft Online](https://go.microsoft.com/fwlink/?LinkId=870457)och [Microsoft Marketplace utgivar avtal](https://go.microsoft.com/fwlink/?linkid=843476).

#### <a name="provide-your-publisher-profile"></a>Ange din utgivar profil

Din utgivar profil innehåller ditt företags namn och ditt MPN-ID. Om du ännu inte har gjort det måste du gå med i [Microsoft Partner Network](https://partner.microsoft.com/commercial). När du har anslutit till Microsoft Partner Network får du ett MPN-ID. 

Skapa ett utgivar-ID. Ditt utgivar-ID identifierar ditt företag och dina erbjudanden i Marketplace och AppSource. 

När du har bekräftat din utgivar profil information godkänner du villkoren och skapar ditt partner Center-konto genom att välja **Godkänn och fortsätt**. 

>[!Important]
>*Du måste ha behörighet att agera på företagets vägnar för att kunna godkänna dessa villkor.*

Tack för att du skapar ett konto på Partner Center! Nu kommer du till sidan för [extern Marketplace-översikt](./commercial-marketplace-overview.md) .

### <a name="publishers-moving-from-cpp"></a>Utgivare som flyttar från CPP

Om ditt konto har migrerats från [Cloud Partner Portal (cpp)](https://cloudpartner.azure.com)behöver du inte skapa ett nytt Partner Center-konto, men kommer att ha fått en anpassad länk till ditt nya partner Center-konto via e-post och i ett informations meddelande som visas efter Logga in på ditt befintliga CPP-konto.

När du har aktiverat ditt nya partner Center-konto genom att gå till den här anpassade länken kan du gå tillbaka till ditt konto genom att gå till [instrument panelen för kommersiellt marknads platser](https://partner.microsoft.com/dashboard/commercial-marketplace/) i Partner Center.

Publicerings avtalet och företags profil informationen kommer att migreras till ditt nya partner Center-konto, tillsammans med tidigare konfigurerade profil information för konto utbetalning, användar konton och behörigheter samt aktiva erbjudanden som är associerade med ditt CPP-konto. 

När din konto information har flyttats från CPP till Partner Center använder du inte längre CPP för att göra konto uppdateringar eller hantera användare, behörigheter och fakturering. Under en begränsad tid uppdateras eventuella konto uppdateringar som du gör i Partner Center automatiskt i ditt skrivskyddade CPP-konto tills CPP-portalen slutligen är föråldrad.

## <a name="add-new-publishers-to-the-commercial-marketplace-program"></a>Lägga till nya utgivare i programmet för kommersiella marknads platser

En organisation kan ha flera utgivare som är kopplade till ett kommersiellt marknads plats konto. En befintlig användare kan lägga till fler utgivare efter att ha loggat in på Partner Center genom att välja **konto inställningar** -> **utgivare** -> **Lägg till utgivare**.

>[!Note]
>Innan du lägger till en ny utgivare kan du granska dina befintliga utgivare genom att logga in på Partner Center och välja **konto inställnings** -> **utgivare** för att se en lista över befintliga utgivare.

En annan användare från samma Azure Active Directory klient organisation kan lägga till en ny utgivare genom att följa stegen nedan:

1. Starta registrerings flödet på [Microsoft Partner Center](https://partner.microsoft.com/en-us/dashboard/account/v3/enrollment/introduction/azureisv).
1. Välj **Logga in med ett arbets konto** och ange din e-postadress till arbetet.
1. Välj knappen **Lägg till utgivare** .
1. Välj det MPN-ID som du vill koppla till utgivaren.
1. Uppdatera **utgivar informationen** i formuläret. <br>

   1. **Utgivar namn**: Det namn som ska visas i Azure Marketplace eller AppSource med erbjudandet. <br>
   1. **PublisherID**: En identifierare som används av Partner Center för att identifiera utgivaren unikt. Standardvärdet för det här fältet mappar till ett befintligt `PublisherID` och unikt i systemet, som inte kan återanvändas, och därför måste det här fältet uppdateras. <br>
   1. **Kontakt information**: Uppdatera kontakt informationen när det behövs.

1. När du har slutfört processen kan du hantera din nyligen skapade utgivare genom att gå till det **kommersiella Marketplace** -programmet som visas i den vänstra navigerings menyn. Om du inte ser det **kommersiella Marketplace** -programmet uppdaterar du sidan.  Den nya utgivaren kommer att visas i listan **utgivare** .

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partner Center](./manage-account.md) 
