---
title: Konto migrering från Cloud Partner Portal till Partner Center – kommersiell marknads plats för Azure
description: Så här migrerar du ditt konto från CPP till Partner Center. – Kommersiell marknads plats för Azure
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 661adb755d076b0cc6114a287855482165ccb817
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208091"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Konto migrering från Cloud Partner Portal till Partner Center

När dina erbjudanden migreras från Cloud Partner Portal (CPP) till Partner Center (PC) blir de låsta för redigering i CPP. I det här läget måste dina konto inställningar migreras till Partner Center. Både dina konto inställningar, och dina erbjudanden, kan hanteras i Partner Center.

## <a name="account-migration-process"></a>Process för konto migrering

När erbjudanden migreras över från CPP är ditt konto konfigurerat för migrering. 
 
Om du är en användare med ägar rollen i CPP för ett specifikt konto visas en gul banderoll på utgivarens profil sida. Du uppmanas att flytta dina konto inställningar till Partner Center. 

Klicka på banderollen för att initiera processen för migrering av konto. Du förväntas ange följande objekt:

### <a name="work-email-address"></a>E-postadress till arbetet

I de flesta fall loggar du in med den e-postadress som du använder för att logga in på CPP. I vissa fall måste en annan e-postadress användas:

* Microsoft-konto: Om kontot CPP är ett Microsoft-konto måste du ange en giltig e-postadress för arbetet som är kopplat till klienten, för vilken MPN-ID: t är registrerat.

* Klient matchnings fel: Om din e-postadress för arbetet inte tillhör den klient som är associerad med det Microsoft Partner Network-ID som finns på ditt CPP-konto visas ett fel meddelande. Om du vill flytta förbi det här felet anger du en e-postadress som är kopplad till klienten. Ett fel meddelande anger namnet på klienten.

### <a name="sign-up-for-microsoft-partner-network-program"></a>Registrera dig för Microsoft Partner Network program

Om ditt CPP-konto inte har något Microsoft Partner Network-ID eller har ett ogiltigt, måste du registrera dig för det Microsoft Partner Network programmet som en del av aktiverings processen.

## <a name="after-account-migration-is-complete"></a>När kontots migrering är slutförd

Migrering måste bara ske en gång för ett angivet konto. När en specifik partner har slutfört migreringen av kontot kommer alla ägare att se detta beteende på utgivar profil sidan:

1. Sidan partner inställningar visas i Microsoft Partner Network, där du nu kan hantera konto inställningar. 
2. En gul banderoll på utgivarens profil sida i CPP visas för användare som har ägar rollen och ber dem att hantera sina konto inställningar i Partner Center. 
3. Sidan konto inställningar i CPP konverteras till skrivskyddat läge. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Flytta Dynamics 365-baserade lösningar till Partner Center

Om du har skapat Dynamics 365 för kund engagemang eller Dynamics 365 för finans-och drift lösningar på en kommersiell partner GTM-Portal **bör dessa lösningar nu hanteras i Partner Center**.

**Om du inte flyttade dina lösningar senast den 31 augusti 2019**slutför du stegen nedan så snart som möjligt. Tills du gör detta: 
- ISV: er har inte till gång till marknadsförings förmåner
- Samtidig försäljning prioriteras förlorar sin status
- De som kräver moln inbäddning kommer inte att uppfylla kraven

> [!NOTE]
> Om ditt MPN-medlemskaps konto ursprungligen skapades i partner Membership Center (PMC) loggar du in på [partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) för att bekräfta att ditt konto har migrerats innan du slutför stegen nedan. Om du ser en profil skärm med ditt MPN-ID är du redo att fortsätta. Om inte, måste du starta migreringen av kontot genom att följa instruktionerna i [partner medlemskaps centret](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Om du behöver hjälp med det här kan [](https://partner.microsoft.com/support?issueid=100-0077)du besöka supporten.

1. Gå till [sidan för extern Marketplace-översikt i Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Om du ser "kommersiell marknads plats" i det vänstra navigerings fönstret är du registrerad och bör fortsätta till nästa steg. Om inte, [Registrera dig på den kommersiella marknads platsen](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) nu.
2. Bekräfta att dina erbjudanden är i AppSource genom att [söka efter dina erbjudanden](https://appsource.microsoft.com/). Om dina erbjudanden redan finns i AppSource går du vidare till nästa steg. För ett erbjudande som inte finns i AppSource skapar du en [ny dynamics 365 för kund engagemang](create-new-customer-engagement-offer.md) eller en [ny Dynamics 365 för drift erbjudande](create-new-operations-offer.md).
3. Verifiera registreringen i Business Applications ISV Connect-program:
  
   * På sidan [avtal](https://partner.microsoft.com/dashboard/account/agreements) i Partner Center ser du till att du har accepterat **Business Applications ISV-tillägget** för att registrera dig i programmet.
   * På sidan [konto inställningar](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) anger du din fakturerings information.

4. Skicka in varje nytt och befintligt erbjudande för certifiering, även om dina erbjudanden tidigare har certifierats. Om det är berättigat kan du begära att delta på Premium nivån under den här processen. Om ditt erbjudande tidigare har certifierats **måste du slutföra appens certifiering senast den 15 oktober 2019.** Certifiering eller certifiering kräver att din app stöder den senaste versionen av vår Business Applications-plattform.
5. Gå till [en kommersiell partner GTM-Portal](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) och Lägg till din AppSource List-URL i Marketplace Länkar-avsnittet. Om du behöver hjälp med det här steget kan du skicka cosell@microsoft.come-post till oss på.

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partner Center](./manage-account.md) 
