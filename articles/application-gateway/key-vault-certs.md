---
title: SSL-avslutning med Key Vault-certifikat
description: Lär dig hur du kan integrera Azure application gateway med Key Vault för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759605"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL-avslutning med Key Vault-certifikat

[Azure Key Vault](../key-vault/key-vault-whatis.md) är en plattform-hanterad lagringsplats för hemligheter du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. Application Gateway stöder integration med Key Vault (i allmänt tillgänglig förhandsversion) för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. Det här stödet är begränsat till v2-SKU för Application Gateway.

> [!IMPORTANT]
> Application Gateway Key Vault-integrering är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns två modeller för SSL-avslutning med den här offentliga förhandsversionen:

- Du kan uttryckligen ange SSL-certifikat som är kopplade till lyssnaren. Det här är den traditionella modellen med skicka SSL-certifikat till Application Gateway för SSL-avslutning.
- Du kan också bifoga en referens till ett befintligt certifikat i Key Vault eller hemlighet under HTTPS aktiverade lyssnaren skapa.

Det finns många fördelar med Key Vault-integrering, inklusive:

- Bättre säkerhet eftersom SSL-certifikat inte hanteras direkt av programmet utvecklingsteam. Integrering med Key Vault ger en separat säkerhetsteam för att etablera, styra livscykel och ge behörighet att välja Application Gateways för åtkomst-certifikat som lagras i Key Vault.
- Stöd för att importera befintliga certifikat till Key Vault eller använda Key Vault-API: er för att skapa och hantera nya certifikat med någon av de betrodda Key Vault-partner.
- Stöd för certifikat som lagras i Key Vault för att förnya dem automatiskt.

Application Gateway stöder för närvarande verifierat programcertifikat endast. Maskinvara security-modul (HSM) verifiera certifikat stöds inte. När Application Gateway har konfigurerats för att använda Key Vault-certifikat kan hämta certifikatet från Key Vault dess instanser och installera dem lokalt för SSL-avslutning. Instanserna avsökningar också regelbundna Key Vault med ett 24-timmars intervall att hämta en förnyade version av certifikat, om den finns. Om ett uppdaterat certifikat hittas, roteras automatiskt SSL-certifikatet som är kopplade till HTTPS-lyssnaren.

## <a name="how-it-works"></a>Hur det fungerar

Integrering med Key Vault kräver en konfigurationsprocessen för tre steg:

1. **Skapa användare som tilldelats hanterad identitet**

   Du måste skapa eller återanvända en befintlig användare som tilldelats hanterad identitet som Application Gateway använder för att hämta certifikat från Key Vault för din räkning. Mer information finns i [vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md) Det här steget skapar en ny identitet i Azure AD-klient som är betrott av den prenumeration som används för att skapa identiteten.
1. **Konfigurera Key Vault**

   Du måste sedan antingen importera eller skapa ett nytt certifikat i Key Vault används av program som körs via Application Gateway. En Key Vault-hemlighet som lösenord utan Base64 kodade PFX-fil kan också användas i det här steget. Med hjälp av en certifikattyp rekommenderas på grund av autoförnyelse funktioner som är tillgängliga med certifikat typobjekt i Key Vault. När ett certifikat eller en hemlighet har skapats, åtkomstprinciper måste definieras i Key Vault för att tillåta identitet som beviljas *hämta* åtkomst att hämta hemligheten.

1. **Konfigurera Application Gateway**

   När de två tidigare stegen har slutförts, kan du etablera eller ändra en befintlig Programgateway för användning av användaren som har tilldelats hanterad identitet. Du också konfigurera HTTP-lyssnaren SSL-certifikat så att den pekar till den fullständiga URI för Key Vault-certifikat eller Hemlig-ID.

![Key Vault-certifikat](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera SSL-avslutning med Key Vault-certifikat med hjälp av Azure PowerShell](configure-keyvault-ps.md).