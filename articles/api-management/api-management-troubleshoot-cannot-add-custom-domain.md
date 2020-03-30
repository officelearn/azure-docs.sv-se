---
title: Det går inte att lägga till anpassad domän med hjälp av Key Vault-certifikat
titleSuffix: Azure API Management
description: Lär dig hur du felsöker problemet där du inte kan lägga till en anpassad domän i Azure API Management med hjälp av ett nyckelvalvscertifikat.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430580"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Det gick inte att uppdatera värdnamn för API Management-tjänsten

I den här artikeln beskrivs felet "Det gick inte att uppdatera API Management-tjänstvärdnamn" som kan uppstå när du lägger till en anpassad domän för Azure API Management-tjänsten. Den här artikeln innehåller felsökningssteg som hjälper dig att lösa problemet.

## <a name="symptoms"></a>Symtom

NÃ¤r du fÃ¶rsÃ¶ks fÃ¶rsÃ¶k att lägga till en anpassad domän fÃ¶r API Management-tjänsten med ett certifikat frÃ¶r Azure Key Vault visas fÃ¶10 felmeddelande:

- Det gick inte att uppdatera VÄRDnamn för API Management-tjänsten. Begäran tillhttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0resursen ' ' misslyckades med StatusCode: Forbidden for RequestId: . Undantagsmeddelande: Åtgärden returnerade en ogiltig statuskod "Förbjudet".

## <a name="cause"></a>Orsak

API Management-tjänsten har inte behörighet att komma åt nyckelvalvet som du försöker använda för den anpassade domänen.

## <a name="solution"></a>Lösning

Följ dessa anvisningar för att lösa problemet:

1. Gå till [Azure-portalen,](Https://portal.azure.com)välj din API Management-instans och välj sedan **Hanterade identiteter**. Kontrollera att alternativet **Registrera med Azure Active Directory** är inställt på **Ja**. 
    ![Registrera dig hos Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. Öppna tjänsten **Nyckelvalv** i Azure-portalen och välj det nyckelvalv som du försöker använda för den anpassade domänen.
1. Välj **Åtkomstprinciper**och kontrollera om det finns ett tjänsthuvudnamn som matchar namnet på API Management-tjänstinstansen. Om det finns väljer du tjänstens huvudnamn och kontrollerar att den har behörigheten **Hämta** under **Hemliga behörigheter**.  
    ![Lägga till åtkomstprincip för tjänstens huvudnamn](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Om API Management-tjänsten inte finns i listan väljer du **Lägg till åtkomstprincip**och skapar sedan följande åtkomstprincip:
    - **Konfigurera från mall:** Ingen
    - **Välj huvudnamn**: Sök i namnet på API Management-tjänsten och välj den sedan i listan
    - **Nyckelbehörigheter**: Ingen
    - **Hemliga behörigheter**: Hämta
    - **Certifikatbehörigheter**: Ingen
1. Välj **OK** om du vill skapa åtkomstprincipen.
1. Välj **Spara** om du vill spara ändringarna.

Kontrollera om problemet är löst. Det gör du genom att försöka skapa den anpassade domänen i API Management-tjänsten med hjälp av Key Vault-certifikatet.

## <a name="next-steps"></a>Nästa steg
Läs mer om API Management-tjänsten:

- Kolla in fler [videor](https://azure.microsoft.com/documentation/videos/index/?services=api-management) om API Management.
* Andra sätt att skydda backend-tjänsten finns i [Autentisering av ömsesidigt certifikat](api-management-howto-mutual-certificates.md).

* [Skapa en API Management-tjänstinstans](get-started-create-service-instance.md).
* [Hantera ditt första API](import-and-publish.md).