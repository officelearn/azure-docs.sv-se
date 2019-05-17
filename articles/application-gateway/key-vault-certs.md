---
title: SSL-avslutning med Azure Key Vault-certifikat
description: Lär dig hur du kan integrera Azure Application Gateway med Key Vault för servercertifikat som är kopplade till HTTPS-aktiverad lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827631"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL-avslutning med Key Vault-certifikat

[Azure Key Vault](../key-vault/key-vault-whatis.md) är en plattform-hanterad hemlighet lagra som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. Azure Application Gateway stöder integration med Key Vault (i allmänt tillgänglig förhandsversion) för servercertifikat som är kopplade till HTTPS-aktiverad lyssnare. Det här stödet är begränsat till v2-SKU för Application Gateway.

> [!IMPORTANT]
> Integrering av Application Gateway med Key Vault finns för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan ett servicenivåavtal (SLA) och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här förhandsversionen finns i två modeller för SSL-avslutning:

- Du kan uttryckligen ange SSL-certifikat som är kopplade till lyssnaren. Den här modellen är det traditionella sättet att skicka SSL-certifikat till Application Gateway för SSL-avslutning.
- Du kan också bifoga en referens till en befintlig Key Vault-certifikat eller en hemlighet när du skapar en lyssnare för HTTPS-aktiverad.

Application Gateway-integration med Key Vault erbjuder många fördelar, inklusive:

- Bättre säkerhet, eftersom SSL-certifikat inte är direkt hanteras av programmet Utvecklingsteamet. Integrering ger en separat säkerhetsteam för att:
  * Ställ in programgatewayer.
  * Styra programlivscykler för gateway.
  * Beviljar behörigheter till valda programgatewayer att komma åt certifikat som lagras i ditt nyckelvalv.
- Stöd för att importera befintliga certifikat till ditt nyckelvalv. Eller Använd Key Vault-API: er för att skapa och hantera nya certifikat med någon av de betrodda Key Vault-partner.
- Stöd för automatisk förnyelse av certifikat som lagras i ditt nyckelvalv.

Application Gateway stöder för närvarande endast programvara-verifierade certifikat. Modul för maskinvarusäkerhet (HSM)-validerade certifikat stöds inte. När Application Gateway har konfigurerats för att använda Key Vault-certifikat kan hämta certifikatet från Key Vault dess instanser och installera dem lokalt för SSL-avslutning. Instanserna avsöka också Key Vault med 24 timmars intervall för att hämta en förnyade version av certifikat, om den finns. Om ett uppdaterat certifikat hittas, roteras automatiskt SSL-certifikatet som är kopplade till HTTPS-lyssnaren.

## <a name="how-integration-works"></a>Så här fungerar integrering

Application Gateway-integration med Key Vault kräver en konfigurationsprocessen för tre steg:

1. **Skapa en hanterad Användartilldelad identitet**

   Du skapar eller återanvända en befintlig användartilldelade hanterad identitet, som Application Gateway använder för att hämta certifikat från Key Vault för din räkning. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md). Det här steget skapar en ny identitet i Azure Active Directory-klient. Identiteten är betrodd av den prenumeration som används för att skapa identiteten.

1. **Konfigurera ditt nyckelvalv**

   Sedan antingen importera ett befintligt certifikat eller skapa en ny i ditt nyckelvalv. Certifikatet ska användas av program som körs via application gateway. I det här steget kan du också använda en hemlighet i nyckelvalvet som lagras som en lösenord utan, base 64-kodad PFX-fil. Vi rekommenderar att du använder en certifikattyp på grund av funktionen för automatisk förnyelse som är tillgängliga med certifikat typobjekt i nyckelvalvet. När du har skapat ett certifikat eller en hemlighet kan du definiera åtkomstprinciper i key vault så att identitet som beviljas *hämta* åtkomst till hemligheten.

1. **Konfigurera application gateway**

   När du har slutfört de två föregående stegen kan du ställa in eller ändra en befintlig Programgateway för att använda den hanterade Användartilldelad identitet. Du kan också konfigurera HTTP-lyssnaren SSL-certifikat så att den pekar till den fullständiga URI för Key Vault-certifikat eller Hemlig-ID.

   ![Key vault-certifikat](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera SSL-avslutning med Key Vault-certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md)
