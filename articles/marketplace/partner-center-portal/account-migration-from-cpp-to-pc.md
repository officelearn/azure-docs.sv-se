---
title: Kontomigrering från Cloud Partner Portal till Partner Center – kommersiell marknadsplats för Azure
description: Så här migrerar du ditt konto från CPP till Partner Center. - kommersiell marknadsplats för Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e17a76d5a017400287644ad2da46caa5b6636654
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262306"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Kontomigrering från Cloud Partner Portal till Partnercenter

Om du har ett befintligt CPP-konto (Cloud Partner Portal) måste dina kontoinställningar migreras till Partner Center.

## <a name="account-migration-process"></a>Processen för kontomigrering

Om du är en användare med rollen Ägare i CPP för ett visst konto visas en gul banderoll på sidan Publisher-profil. Du kan tillhöra ett av följande två fall:

- Ditt konto har redan migrerats och du är redo att hantera dina kontoinställningar i Partnercenter.
- Ditt konto har inte migrerats till Partner Center och du måste vidta ytterligare åtgärder.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Ditt konto har migrerats till Partner center

För alla konton som har slutfört migreringen från CPP till Partner Center sker kontohantering i Partner Center. Ändringar som tillägg/borttagning av användare synkroniseras tillbaka till CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Du har ännu inte migrerat ditt konto till Partner Center

Klicka på bannern för att starta din kontomigreringsprocess. Du förväntas ange följande:

1. E-postadress för arbete: <br> <br> I de flesta fall loggar du in med den e-postadress som du använder för att logga in på CPP. I vissa fall måste en annan e-postadress användas:

    * Microsoft-konto: Om CPP-kontot är ett Microsoft-konto anger du ett giltigt arbetsmeddelande som är kopplat till den klient som Microsoft Partner Network-ID:t (MICROSOFT Partner Network) är registrerat för. Mer information finns i [Registrera dig för Microsoft Partner Network Program](#sign-up-for-microsoft-partner-network-program).

    * Klienten matchar inte: Om din e-postadress för arbete inte tillhör klienten som är associerad med Microsoft Partner Network ID som finns på ditt CPP-konto visas ett fel. Om du vill gå förbi det här felet anger du en e-postadress som är associerad med klienten. Ett felmeddelande ger namnet på klienten.

2. Registrera dig för Microsoft Partner Network-programmet

    Om ditt CPP-konto inte har ett Microsoft Partner Network-ID eller har ett som är ogiltigt måste du registrera dig för Microsoft Partner Network-programmet som en del av aktiveringsprocessen.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registrera dig för Microsoft Partner Network-programmet

Företag som vill samarbeta med Microsoft måste gå med i Microsoft Partner Network (MPN) och få ett MPN-ID. Om du redan är medlem i Microsoft Partner Network och har ett MPN-ID behåller du informationen till hands eftersom du behöver den under kontoaktiveringsprocessen.  

Om du inte är medlem i Microsoft Partner Network kan du [gå med här](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) för att få ett MPN-ID. Anteckna ditt MPN-ID eftersom du måste ange det under kontoaktiveringsprocessen.

Mer information om Microsoft Partner Network finns [i Gå med i Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) på partnerwebbplatsen. Mer information om ISV-fördelar i Microsoft Partner Network finns i [ISV Resource Hub](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Flytta Dynamics 365- och PowerApps-erbjudanden till Partner Center

För att effektivisera konto- och erbjudandehantering för Dynamics 365 Customer Engagement, PowerApps och Dynamics 365 Operations har erbjudanden flyttats till [Partner Center](https://partner.microsoft.com/). Flytten säkerställer att samma innehåll är tillgängligt för både offentliga kataloger och säljarkataloger.

För specifik information om vad som behöver göras senast **den 15 oktober 2019** för dina Dynamics 365 Customer Engagement-, PowerApps- och Dynamics 365 Operations-erbjudanden, följ instruktionerna nedan.

> [!NOTE]
> Detta gäller inte för Dynamics 365 Business Central-erbjudanden.  

1. Om ditt MPN-medlemskapskonto ursprungligen skapades i Partner Membership Center (PMC) loggar du in på [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) för att bekräfta att ditt konto har migrerats. Om du ser en profilskärm med mpn-ID:t är du redo att fortsätta. Om inte, måste du starta din kontomigrering genom att följa anvisningarna i [partnermedlemskapscentret](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Om du behöver hjälp, besök [support](https://partner.microsoft.com/support?issueid=100-0077).
2. Gå till [översiktssidan för Kommersiella marknadsplatser i Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Om du ser "Kommersiell marknadsplats" i det vänstra navigeringsfönstret registreras du och bör fortsätta till nästa steg. Om inte, [registrera dig på den kommersiella marknaden](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) nu.
3. Bekräfta att dina erbjudanden finns i AppSource genom [att söka efter dina erbjudanden](https://appsource.microsoft.com/). Om dina erbjudanden redan finns i AppSource fortsätter du till nästa steg. Skapa ett [nytt Dynamics 365 Customer Engagement-erbjudande](create-new-customer-engagement-offer.md) eller ett [nytt Dynamics 365 Operations-erbjudande](create-new-operations-offer.md)för alla erbjudanden som inte ingår i AppSource.
4. På [sidan Avtal i](https://partner.microsoft.com/dashboard/account/agreements)Partnercenter kontrollerar du att du har granskat och godkänt **ISV-tillägget för företagsprogram**.
5. Kontrollera att faktureringsinformationen är klar i Partnercentrets [kontoinställningar.](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)
6. Skicka in varje nytt och befintligt erbjudande för certifiering och publicering, även om dina erbjudanden tidigare var certifierade.
    * Fyll i informationsskärmarna, inklusive att tillhandahålla din app för certifiering, samt marknadsföringsinformation. Välj **Skicka** (längst upp till höger på skärmen) senast den **15 oktober 2019**. Dessa steg måste slutföras för att undvika att det påverkar erbjudandets tillgänglighet.
    * Om du är berättigad kan du begära att få delta i premiumnivån under den här processen.
    * Certifiering eller omcertifiering kräver att din app stöder den senaste versionen av vår plattform för affärsprogram.
    * När din app har godkänts får du ett e-postmeddelande för att återgå till erbjudandet och välja "gå live" för att erbjudandet ska kunna visas på Microsoft AppSource.

## <a name="additional-resources"></a>Ytterligare resurser

Gå med i det veckovisa [Dynamics ISV-communitysamtalet](https://aka.ms/DynamicsISV-CommunityCall) för support och uppdateringar.

Om du behöver hjälp med att publicera, certifiera eller hantera marknadsplatserbjudanden [skickar du en supportbiljett.](https://aka.ms/MarketplacePublisherSupport)

## <a name="next-steps"></a>Nästa steg

- [Hantera ditt kommersiella marketplace-konto i Partner Center](./manage-account.md)
