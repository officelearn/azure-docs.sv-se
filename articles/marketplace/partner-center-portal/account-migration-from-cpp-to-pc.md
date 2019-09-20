---
title: Konto migrering från Cloud Partner Portal till Partner Center – kommersiell marknads plats för Azure
description: Så här migrerar du ditt konto från CPP till Partner Center. – Kommersiell marknads plats för Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147128"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Konto migrering från Cloud Partner Portal till Partner Center

Om du har ett befintligt CPP-konto måste dina konto inställningar migreras till Partner Center.

## <a name="account-migration-process"></a>Process för konto migrering

Om du är en användare med ägar rollen i CPP för ett specifikt konto visas en gul banderoll på utgivarens profil sida. Du kan höra till något av följande två fall:

- Ditt konto har redan migrerats och du är redo att hantera dina konto inställningar i Partner Center.
- Du måste vidta ytterligare åtgärder för att slutföra migreringen av kontot till Partner Center.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Ditt konto har migrerats till Partner Center

För alla konton som har slutfört migreringen från CPP till Partner Center, sker konto hantering i Partner Center. Ändringar som användar tillägg/borttagning kommer att synkroniseras tillbaka till CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Du har ännu inte migrerat ditt konto till Partner Center

Klicka på banderollen för att initiera processen för migrering av konto. Du förväntas ange följande objekt:

1. E-postadress till arbetet: <br> <br> I de flesta fall loggar du in med den e-postadress som du använder för att logga in på CPP. I vissa fall måste en annan e-postadress användas:

    * Microsoft-konto: Om kontot CPP är ett Microsoft-konto måste du ange en giltig e-postadress för arbetet som är kopplat till klienten, för vilken MPN-ID: t är registrerat.

    * Klient matchnings fel: Om din e-postadress för arbetet inte tillhör den klient som är associerad med det Microsoft Partner Network-ID som finns på ditt CPP-konto visas ett fel meddelande. Om du vill flytta förbi det här felet anger du en e-postadress som är kopplad till klienten. Ett fel meddelande anger namnet på klienten.

2. Registrera dig för Microsoft Partner Network program

    Om ditt CPP-konto inte har något Microsoft Partner Network-ID eller har ett ogiltigt, måste du registrera dig för det Microsoft Partner Network programmet som en del av aktiverings processen.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registrera dig för Microsoft Partner Network program

Företag som vill ha en partner med Microsoft måste gå med i Microsoft Partner Network (MPN) och hämta ett MPN-ID. Om du redan är medlem i Microsoft Partner Network och har ett MPN-ID, måste du ha det på handen eftersom du måste ange det under konto aktiverings processen.  

Om du ännu inte är medlem i Microsoft Partner Network kan du [ansluta nu](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) för att få ett MPN-ID. Se till att anteckna ditt MPN-ID eftersom du måste ange det under konto aktiverings processen.

Mer information om Microsoft Partner Network finns i [delta i Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) på partner webbplatsen. Mer information om hur du ansluter till Microsoft Partner Network Benefits-ISV: ar finns i [ISV-instansresursen](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Flytta Dynamics 365-baserade lösningar till Partner Center

Om du har skapat Dynamics 365 för kund engagemang eller Dynamics 365 för finans-och drift lösningar på en kommersiell partner GTM-Portal **bör dessa lösningar nu hanteras i Partner Center**.

**Om du inte flyttade dina lösningar senast den 31 augusti 2019**slutför du stegen nedan så snart som möjligt. Tills du gör detta:

- ISV: er har inte till gång till marknadsförings förmåner
- Samtidig försäljning prioriteras förlorar sin status
- De som kräver moln inbäddning upphör att följa efter den 15 oktober 2019

> [!NOTE]
> Om ditt MPN-medlemskaps konto ursprungligen skapades i partner Membership Center (PMC) loggar du in på [partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) för att bekräfta att ditt konto har migrerats innan du slutför stegen nedan. Om du ser en profil skärm med ditt MPN-ID är du redo att fortsätta. Om inte, måste du starta migreringen av kontot genom att följa instruktionerna i [partner medlemskaps centret](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Om du behöver hjälp med det här kan du besöka [supporten](https://partner.microsoft.com/support?issueid=100-0077).

1. Gå till [sidan för extern Marketplace-översikt i Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Om du ser "kommersiell marknads plats" i det vänstra navigerings fönstret är du registrerad och bör fortsätta till nästa steg. Om inte, [Registrera dig på den kommersiella marknads platsen](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) nu.

2. Bekräfta att dina erbjudanden är i AppSource genom att [söka efter dina erbjudanden](https://appsource.microsoft.com/). Om dina erbjudanden redan finns i AppSource går du vidare till nästa steg. För ett erbjudande som inte finns i AppSource skapar du en [ny dynamics 365 för kund engagemang](create-new-customer-engagement-offer.md) eller en [ny Dynamics 365 för drift erbjudande](create-new-operations-offer.md).

3. Verifiera registreringen i Business Applications ISV Connect-program:
   * På sidan [avtal](https://partner.microsoft.com/dashboard/account/agreements) i Partner Center ser du till att du har accepterat **Business Applications ISV-tillägget** för att registrera dig i programmet.
   * På sidan [konto inställningar](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) anger du din fakturerings information.

4. Skicka in varje nytt och befintligt erbjudande för certifiering, även om dina erbjudanden tidigare har certifierats. Om det är berättigat kan du begära att delta på Premium nivån under den här processen. Om ditt erbjudande tidigare har certifierats **måste du slutföra appens certifiering senast den 15 oktober 2019.** Certifiering eller certifiering kräver att din app stöder den senaste versionen av vår Business Applications-plattform.

5. Gå till [en kommersiell partner GTM-Portal](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) och Lägg till din AppSource List-URL i Marketplace Länkar-avsnittet. Om du behöver hjälp med det här steget kan du skicka cosell@microsoft.come-post till oss på.

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partner Center](./manage-account.md) 
