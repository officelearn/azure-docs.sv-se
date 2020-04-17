---
title: TLS-avslutning med Azure Key Vault-certifikat
description: Lär dig hur du kan integrera Azure Application Gateway med Key Vault för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: be558c3e3a68ce6c194dcf98d8f5ff92c4c14edb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457832"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-avslutning med Key Vault-certifikat

[Azure Key Vault](../key-vault/general/overview.md) är ett plattformshanterad hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och TLS/SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. Det här stödet är begränsat till v2 SKU för Application Gateway.

Key Vault-integrering erbjuder två modeller för TLS-avslutning:

- Du kan uttryckligen ange TLS/SSL-certifikat som är kopplade till lyssnaren. Den här modellen är det traditionella sättet att skicka TLS/SSL-certifikat till Application Gateway för TLS-avslutning.
- Du kan också ange en referens till ett befintligt Key Vault-certifikat eller en hemlighet när du skapar en HTTPS-aktiverad lyssnare.

Application Gateway-integrering med Key Vault erbjuder många fördelar, bland annat:

- Starkare säkerhet eftersom TLS/SSL-certifikat inte hanteras direkt av programutvecklingsteamet. Med integrering kan ett separat säkerhetsteam:
  * Konfigurera programgateways.
  * Styr livscykeln för programgateway.
  * Bevilja behörigheter till valda programgateways för att komma åt certifikat som lagras i nyckelvalvet.
- Stöd för att importera befintliga certifikat till nyckelvalvet. Eller använd Key Vault-API:er för att skapa och hantera nya certifikat med någon av de betrodda Key Vault-partnerna.
- Stöd för automatisk förnyelse av certifikat som lagras i nyckelvalvet.

Application Gateway stöder endast programvaruvaliderade certifikat. HSM-verifierade certifikat (Hardware Security Module) stöds inte. När Application Gateway har konfigurerats för att använda Key Vault-certifikat hämtar dess instanser certifikatet från Key Vault och installerar dem lokalt för TLS-avslutning. Instanserna avsöker också Key Vault med 24-timmarsintervall för att hämta en förnyad version av certifikatet, om det finns. Om ett uppdaterat certifikat hittas roteras det TLS/SSL-certifikat som för närvarande är associerat med HTTPS-lyssnaren automatiskt.

> [!NOTE]
> Azure-portalen stöder bara KeyVault-certifikat, inte hemligheter. Application Gateway stöder fortfarande referera hemligheter från KeyVault, men bara genom icke-Portal-resurser som PowerShell, CLI, API, ARM-mallar, etc. 

## <a name="how-integration-works"></a>Så här fungerar integreringen

Integrering av programgateway med Key Vault kräver en konfigurationsprocess i tre steg:

1. **Skapa en användartilldelad hanterad identitet**

   Du skapar eller återanvänder en befintlig användartilldelad hanterad identitet som Application Gateway använder för att hämta certifikat från Key Vault för din räkning. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md). Det här steget skapar en ny identitet i Azure Active Directory-klienten. Identiteten är betrodd av prenumerationen som används för att skapa identiteten.

1. **Konfigurera nyckelvalvet**

   Du importerar sedan antingen ett befintligt certifikat eller skapar ett nytt i nyckelvalvet. Certifikatet används av program som körs via programgatewayen. I det här steget kan du också använda en nyckelvalvshemlighet som lagras som en lösenordslös, bas 64-kodad PFX-fil. Vi rekommenderar att du använder en certifikattyp på grund av funktionen för automatisk förnyelse som är tillgänglig med certifikattypsobjekt i nyckelvalvet. När du har skapat ett certifikat eller en hemlighet definierar du åtkomstprinciper i nyckelvalvet så att identiteten kan beviljas *få* åtkomst till hemligheten.

1. **Konfigurera progamgatewayen**

   När du har slutfört de två föregående stegen kan du konfigurera eller ändra en befintlig programgateway för att använda den användartilldelade hanterade identiteten. Du kan också konfigurera HTTP-lyssnarens TLS/SSL-certifikat så att det pekar på hela URI-certifikatet för Key Vault eller hemligt ID.

   ![Certifikat för nyckelvalv](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera TLS-avslutning med Key Vault-certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md)
