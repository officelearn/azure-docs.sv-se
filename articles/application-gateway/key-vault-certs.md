---
title: TLS-avslutning med Azure Key Vault certifikat
description: Lär dig hur du kan integrera Azure Application Gateway med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 934cf854b0c526ed994c7dc91763f65de64fd14b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617516"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS-avslutning med Key Vault certifikat

[Azure Key Vault](../key-vault/general/overview.md) är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och TLS/SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till v2-SKU: n för Application Gateway.

Key Vault integration erbjuder två modeller för att avsluta TLS:

- Du kan uttryckligen tillhandahålla TLS/SSL-certifikat som är kopplade till lyssnaren. Den här modellen är det traditionella sättet att skicka TLS/SSL-certifikat till Application Gateway för TLS-avslutning.
- Du kan också ange en referens till ett befintligt Key Vault certifikat eller hemlighet när du skapar en HTTPS-aktiverad lyssnare.

Application Gateway integration med Key Vault erbjuder många fördelar, inklusive:

- Starkare säkerhet, eftersom TLS/SSL-certifikat inte hanteras direkt av program utvecklings teamet. Integrationen gör att ett separat säkerhets team kan:
  * Konfigurera programgatewayer.
  * Kontrol lera Application Gateway-livscykler.
  * Bevilja behörighet till valda programgatewayer för att komma åt certifikat som lagras i ditt nyckel valv.
- Stöd för import av befintliga certifikat i nyckel valvet. Eller Använd Key Vault API: er för att skapa och hantera nya certifikat med någon av de betrodda Key Vault partnerna.
- Stöd för automatisk förnyelse av certifikat som lagras i ditt nyckel valv.

Application Gateway stöder för närvarande endast program varu validerade certifikat. HSM (Hardware Security Module) – verifierade certifikat stöds inte. När Application Gateway har kon figurer ATS för att använda Key Vault certifikat, hämtar dess instanser certifikatet från Key Vault och installerar dem lokalt för TLS-avslutning. Instanserna avsöker också Key Vault med 24 timmar för att hämta en förnyad version av certifikatet, om det finns. Om ett uppdaterat certifikat hittas, roteras TLS/SSL-certifikatet som för närvarande är associerat med HTTPS-lyssnaren automatiskt.

> [!NOTE]
> Azure Portal stöder bara certifikat för nyckel valv, inte hemligheter. Application Gateway stöder fortfarande referenser till hemligheter från nyckel valv, men endast via icke-Portal resurser som PowerShell, CLI, API, ARM-mallar osv. 

## <a name="how-integration-works"></a>Så här fungerar integreringen

Application Gateway-integrering med Key Vault kräver en konfigurations process i tre steg:

1. **Skapa en användartilldelad hanterad identitet**

   Du skapar eller återanvänder en befintlig användare som tilldelats en hanterad identitet, som Application Gateway använder för att hämta certifikat från Key Vault åt dig. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md). I det här steget skapas en ny identitet i Azure Active Directory-klienten. Identiteten är betrodd av den prenumeration som används för att skapa identiteten.

1. **Konfigurera nyckel valvet**

   Du kan antingen importera ett befintligt certifikat eller skapa ett nytt i ditt nyckel valv. Certifikatet kommer att användas av program som körs via programgatewayen. I det här steget kan du också använda en nyckel valvs hemlighet som lagras som en lösen ords fri, bas-64-kodad PFX-fil. Vi rekommenderar att du använder en certifikat typ på grund av den funktion för förnyad förnyelse som är tillgänglig med certifikat typs objekt i nyckel valvet. När du har skapat ett certifikat eller en hemlighet definierar du åtkomst principer i nyckel valvet för att ge identiteten behörighet att *få* åtkomst till hemligheten.
   
   > [!NOTE]
   > Om du distribuerar programgatewayen via en ARM-mall, antingen med hjälp av Azure CLI eller PowerShell, eller via en Azure Application som distribueras från Azure Portal, måste SSL-certifikatet som lagras i nyckel valvet som en Base-64-kodad PFX-fil **vara lösenordsskyddad**. Du måste också utföra stegen i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](../azure-resource-manager/templates/key-vault-parameter.md). Det är särskilt viktigt att ställa `enabledForTemplateDeployment` in `true`på.

1. **Konfigurera progamgatewayen**

   När du har slutfört de två föregående stegen kan du konfigurera eller ändra en befintlig Programgateway för att använda den tilldelade hanterade identiteten. Du kan också konfigurera HTTP-lyssnarens TLS/SSL-certifikat så att det pekar på den fullständiga URI: n för Key Vault certifikat eller hemligt ID.

   ![Key Vault-certifikat](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera TLS-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md)
