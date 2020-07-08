---
title: Hanterade identiteter i Azure SignalR service
description: Lär dig hur hanterade identiteter fungerar i Azure SignalR service och hur du använder en hanterad identitet i Server lösa scenarier.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: abe7503e7eb73d533ae901af21de001960173fb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559416"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Hanterade identiteter för Azure SignalR service

Den här artikeln visar hur du skapar en hanterad identitet för Azure SignalR-tjänsten och hur du använder den i Server lös scenarier.

> [!Important] 
> Azure SignalR service kan bara stödja en hanterad identitet. Det innebär att du kan lägga till antingen en tilldelad identitet eller en tilldelad identitet. 

## <a name="add-a-system-assigned-identity"></a>Lägg till en tilldelad identitet

Om du vill konfigurera en hanterad identitet i Azure Portal skapar du först en Azure SignalR-tjänstinstans och aktiverar sedan funktionen.

1. Skapa en Azure SignalR-tjänstinstans i portalen precis som vanligt. Bläddra till den i portalen.

2. Välj **identitet**.

4. Växla **status** till **på**på fliken **systemtilldelad** . Välj **Spara**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Lägga till en tilldelad identitet i portalen":::

## <a name="add-a-user-assigned-identity"></a>Lägg till en användardefinierad identitet

Om du skapar en Azure SignalR-tjänstinstans med en tilldelad identitet måste du skapa identiteten och sedan lägga till dess resurs-ID i din tjänst.

1. Skapa en användare som tilldelats en hanterad identitets resurs enligt [dessa anvisningar](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Skapa en Azure SignalR-tjänstinstans i portalen precis som vanligt. Bläddra till den i portalen.

3. Välj **identitet**.

4. På fliken **användare tilldelad** väljer du **Lägg till**.

5. Sök efter den identitet som du skapade tidigare och markera den. Välj **Lägg till**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Lägg till en användardefinierad identitet i portalen":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Använda en hanterad identitet i Server lös scenarier

Azure SignalR service är en fullständigt hanterad tjänst, så du kan inte använda en hanterad identitet för att hämta token manuellt. I stället använder Azure SignalR service den hanterade identitet som du har angett för att hämta en åtkomsttoken. Tjänsten ställer sedan in åtkomsttoken i en `Authorization` rubrik i en överordnad begäran i Server lös scenarier.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Aktivera hanterad identitets autentisering i överordnade inställningar

1. Lägg till en tilldelad identitet eller användardefinierad identitet.

2. Konfigurera överordnade inställningar och Använd **ManagedIdentity** **som autentiseringsinställningar.** Information om hur du skapar överordnade inställningar med autentisering finns i [Inställningar för överordnade strömmar](concept-upstream.md).

3. I inställningarna för hanterad identitetsautentisering för **resurs**kan du ange mål resursen. Resursen blir ett `aud` anspråk i den hämtade åtkomsttoken, som kan användas som en del av verifieringen i dina överordnade slut punkter. Resursen kan vara något av följande:
    - Tom
    - Program (klient) ID för tjänstens huvud namn
    - Program-ID-URI för tjänstens huvud namn
    - [Resurs-ID för en Azure-tjänst](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Om du validerar en åtkomsttoken själv i tjänsten kan du välja något av resurs formaten. Se bara till att **resurs** svärdet i **autentiseringsinställningarna** och verifieringen är konsekventa. Om du använder rollbaserad åtkomst kontroll (RBAC) för ett data plan måste du använda den resurs som tjänst leverantören begär.

### <a name="validate-access-tokens"></a>Verifiera åtkomsttoken

Token i `Authorization` rubriken är en åtkomsttoken för [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/access-tokens#validating-tokens).

För att verifiera åtkomsttoken bör din app också verifiera mål gruppen och signerings-token. Dessa måste verifieras mot värdena i OpenID identifierings dokument. Se till exempel [klientens oberoende version av dokumentet](https://login.microsoftonline.com/common/.well-known/openid-configuration).

Den Azure Active Directory (Azure AD) mellanprogram har inbyggda funktioner för att verifiera åtkomsttoken. Du kan bläddra igenom våra [exempel](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) för att hitta ett på valfritt språk.

Vi tillhandahåller bibliotek och kod exempel som visar hur du hanterar verifiering av token. Det finns också flera partner bibliotek med öppen källkod som är tillgängliga för JSON Web Token (JWT)-verifiering. Det finns minst ett alternativ för nästan alla plattformar och språk. Mer information om Azure AD-autentiseringspaket och kod exempel finns i [Microsoft Identity Platform Authentication libraries](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries).

## <a name="next-steps"></a>Nästa steg

- [Azure Functions-utveckling och -konfiguration med Azure SignalR Service](signalr-concept-serverless-development-config.md)
