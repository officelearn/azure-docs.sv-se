---
title: Hanterade identiteter i Azure SignalR service
description: Lär dig hur hanterade identiteter fungerar i Azure SignalR-tjänsten, konfigurera för att använda hanterad identitet i Server lösa scenarier.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: 8e122be74d623c7f1ea5576a5fe527dfa538d1d1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988715"
---
# <a name="how-to-use-managed-identities-for-azure-signalr-service"></a>Använda hanterade identiteter för Azure SignalR service

Det här avsnittet visar hur du skapar en hanterad identitet för Azure SignalR-tjänsten och hur du använder den i Server lös scenarier.

> [!Important] 
> Azure SignalR service kan bara stödja en hanterad identitet. Det innebär att du kan lägga till antingen en tilldelad identitet eller en användardefinierad identitet. 

## <a name="add-a-system-assigned-identity"></a>Lägg till en tilldelad identitet

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterad identitet i portalen skapar du först en signal tjänst som normal och aktiverar sedan funktionen.

1. Skapa en signal tjänst i portalen precis som vanligt. Gå till den i portalen.

2. Välj **identitet**.

4. Växla **status** till **på på**fliken **systemtilldelad** . Klicka på **Spara**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Lägg till systemtilldelad identitet i portalen":::

## <a name="add-a-user-assigned-identity"></a>Lägg till en användardefinierad identitet

Om du skapar en signal tjänst med en användardefinierad identitet måste du skapa identiteten och sedan lägga till dess resurs-ID i din tjänst.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Först måste du skapa en användardefinierad identitets resurs.

1. Skapa en användare som tilldelats en hanterad identitets resurs enligt [dessa anvisningar](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Skapa en signal tjänst i portalen precis som vanligt. Gå till den i portalen.

3. Välj **identitet**.

4. Klicka på **Lägg till**i fliken **tilldelade användare** .

5. Sök efter den identitet som du skapade tidigare och markera den. Klicka på **Lägg till**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Lägg till assigened identitet i portalen":::

## <a name="use-managed-identity-in-serverless-scenarios"></a>Använda hanterad identitet i Server lös scenarier

Som SignalR service är en helt hanterad tjänst, så du kan inte använda hanterad identitet för att hämta token manuellt. I stället använder signal tjänsten den hanterade identitet som du har angett för att hämta åtkomsttoken och vara inställt på `Authorization` huvud i en överordnad begäran i Server lös scenarier.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Aktivera hanterad identitets autentisering i överordnade inställningar

1. Lägg till en tilldelad identitet eller användardefinierad identitet

2. Konfigurera överordnade inställningar och Använd *ManagedIdentity* *som autentiseringsinställningar.* Hitta hur du skapar överordnade inställningar med autentisering vid [överordnade inställningar](concept-upstream.md#create-upstream-settings).

3. I inställningarna för hanterad identitetsautentisering kan du ange mål *resursen*. *Resursen* kommer att bli `aud` anspråk i den hämtade åtkomsttoken, som kan användas som en del av verifieringen i dina överordnade slut punkter. *Resursen* kan vara något av följande
    - Tom
    - Program (klient) ID för tjänstens huvud namn
    - Program-ID-URI för tjänstens huvud namn
    - Resurs-ID för Azure-tjänster, se [resurs-ID för Azure-tjänster](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Om du verifierar åtkomsttoken själv i din tjänst kan du välja något av *resurs* formaten. Så länge för att se till att *resursen* i *autentiseringsinställningarna* och verifieringen är konsekvent. Om du använder data planet RBAC måste du använda den *resurs* som tjänst leverantör begär.

### <a name="validate-access-token"></a>Verifiera åtkomsttoken

Token i `Authorization` rubrik är en åtkomsttoken för [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

För att verifiera åtkomsttoken bör din app också verifiera mål gruppen och signerings-token. Dessa måste verifieras mot värdena i OpenID identifierings dokument. Till exempel finns den klient oberoende versionen av dokumentet på [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration) .

Azure AD-har inbyggda funktioner för att verifiera åtkomsttoken och du kan bläddra igenom våra [exempel](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) för att hitta ett på valfritt språk.

Vi tillhandahåller bibliotek och kod exempel som visar hur du hanterar verifiering av token. Informationen nedan tillhandahålls för de som vill förstå den underliggande processen. Det finns också flera tredjeparts bibliotek med öppen källkod som är tillgängliga för JWT-validering. det finns minst ett alternativ för nästan alla plattformar och språk som finns där. Mer information om Azure AD-autentiseringspaket och kod exempel finns i [v 2.0-autentiseringsinställningar](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Nästa steg

- [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](signalr-concept-serverless-development-config.md)
