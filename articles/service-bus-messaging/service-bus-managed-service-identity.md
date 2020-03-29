---
title: Hanterade identiteter för Azure-resurser med Service Bus
description: I den här artikeln beskrivs hur du använder hanterade identiteter för att komma åt Azure Service Bus-entiteter (köer, ämnen och prenumerationer).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 89de6bf80d14ec77fe6b1f98b6e1d15c6e573fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756291"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för att komma åt Azure Service Bus-resurser
[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en cross-Azure-funktion som gör att du kan skapa en säker identitet som är associerad med distributionen under vilken programkoden körs. Du kan sedan associera den identiteten med åtkomstkontrollroller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som ditt program behöver.

Med hanterade identiteter hanterar Azure-plattformen den här körningsidentiteten. Du behöver inte lagra och skydda åtkomstnycklar i programkoden eller konfigurationen, varken för själva identiteten eller för de resurser du behöver komma åt. En Service Bus-klientapp som körs i ett Azure App Service-program eller på en virtuell dator med aktiverade hanterade entiteter för Azure-resurssupport behöver inte hantera SAS-regler och -nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadressen för namnområdet Service Bus Messaging. När appen ansluter binder Service Bus den hanterade entitetens kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. När den är associerad med en hanterad identitet kan din Service Bus-klient utföra alla auktoriserade åtgärder. Auktorisering beviljas genom att associera en hanterad entitet med Service Bus-roller. 

## <a name="overview"></a>Översikt
När ett säkerhetsobjekt (en användare, grupp eller ett program) försöker komma åt en Service Bus-enhet måste begäran godkännas. Med Azure AD är åtkomst till en resurs en tvåstegsprocess. 

 1. Först autentiseras säkerhetsobjektets identitet och en OAuth 2.0-token returneras. Resursnamnet som begär en `https://servicebus.azure.net`token är .
 1. Därefter skickas token som en del av en begäran till servicebusstjänsten om att auktorisera åtkomst till den angivna resursen.

Autentiseringssteget kräver att en programbegäran innehåller en OAuth 2.0-åtkomsttoken vid körning. Om ett program körs inom en Azure-entitet, till exempel en Azure VM, en skaluppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att komma åt resurserna. 

Auktoriseringssteget kräver att en eller flera RBAC-roller tilldelas säkerhetsobjektet. Azure Service Bus tillhandahåller RBAC-roller som omfattar behörighetsuppsättningar för Service Bus-resurser. De roller som tilldelas ett säkerhetsobjekt avgör vilka behörigheter huvudmannen ska ha. Mer information om hur du tilldelar RBAC-roller till Azure Service Bus finns [i Inbyggda RBAC-roller för Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Inbyggda program och webbprogram som gör förfrågningar till Service Bus kan också auktorisera med Azure AD. Den här artikeln visar hur du begär en åtkomsttoken och använder den för att auktorisera begäranden om Service Bus-resurser. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomsträttigheter
Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). Azure Service Bus definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga behörighetsuppsättningar som används för att komma åt Service Bus-entiteter och du kan också definiera anpassade roller för åtkomst till data.

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen eller servicebussnamnområdet. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en hanterad identitet för Azure-resurser.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Inbyggda RBAC-roller för Azure Service Bus
För Azure Service Bus är hanteringen av namnområden och alla relaterade resurser via Azure-portalen och Azure-resurshanterings-API:et redan skyddad med hjälp av den *rollbaserade åtkomstkontrollmodellen* (RBAC). Azure tillhandahåller nedanstående inbyggda RBAC-roller för att auktorisera åtkomst till ett servicebussnamnområde:

- [Azure Service Bus Data Owner](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Aktiverar dataåtkomst till Service Bus-namnområdet och dess entiteter (köer, ämnen, prenumerationer och filter)
- [Azure Service Bus Data Sender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Använd den här rollen för att ge skicka åtkomst till Service Bus-namnområde och dess entiteter.
- [Azure Service Bus Data Receiver](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Använd den här rollen för att ge mottagande åtkomst till Service Bus-namnområde och dess entiteter. 

## <a name="resource-scope"></a>Resursomfattning 
Innan du tilldelar en RBAC-roll till ett säkerhetsobjekt bestämmer du omfattningen av åtkomsten som säkerhetsobjektet ska ha. Bästa praxis dikterar att det alltid är bäst att bara bevilja minsta möjliga omfattning.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Service Bus-resurser, med början med det smalaste omfånget:

- **Kö,** **ämne**eller **prenumeration**: Rolltilldelning gäller för den specifika Service Bus-entiteten. Azure-portalen stöder för närvarande inte tilldelning av användare/grupper/hanterade identiteter till Service Bus RBAC-roller på prenumerationsnivå. Här är ett exempel på hur du använder Azure [CLI-kommandot: az-role-assignment-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) för att tilldela en identitet till en Service Bus RBAC-roll: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus namnområde**: Rolltilldelning sträcker sig över hela topologin för Service Bus under namnområdet och till konsumentgruppen som är associerad med den.
- **Resursgrupp**: Rolltilldelning gäller för alla Service Bus-resurser under resursgruppen.
- **Prenumeration**: Rolltilldelning gäller för alla Service Bus-resurser i alla resursgrupper i prenumerationen.

> [!NOTE]
> Tänk på att RBAC-rolltilldelningar kan ta upp till fem minuter att sprida. 

Mer information om hur inbyggda roller definieras finns i [Förstå rolldefinitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade RBAC-roller finns i [Skapa anpassade roller för Azure Role-Based Access Control](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Service Bus-resurser från den virtuella datorn måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure-portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-klientbibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörigheter till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran till Service Bus-tjänsten från en hanterad identitet i ditt program konfigurerar du först RBAC-inställningar (Role-Based Access Control) för den hanterade identiteten. Azure Service Bus definierar RBAC-roller som omfattar behörigheter för att skicka och läsa från Service Bus. När RBAC-rollen tilldelas en hanterad identitet beviljas den hanterade identiteten åtkomst till Service Bus-entiteter i lämpligt omfång.

Mer information om hur du tilldelar RBAC-roller finns i [Autentisera och auktorisera med Azure Active Directory för åtkomst till Service Bus-resurser](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Använda Service Bus med hanterade identiteter för Azure-resurser
Om du vill använda Service Bus med hanterade identiteter måste du tilldela rollen och lämpligt scope. Proceduren i det här avsnittet använder ett enkelt program som körs under en hanterad identitet och har åtkomst till Service Bus-resurser.

Här använder vi ett exempel på ett webbprogram som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar ett webbprogram finns [i Skapa en ASP.NET Core-webbapp i Azure](../app-service/app-service-web-get-started-dotnet.md)

När programmet har skapats gör du så här: 

1. Gå till **Inställningar** och välj **Identitet**. 
1. Välj den **status som** ska vara **på**. 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en webbapp](./media/service-bus-managed-service-identity/identity-web-app.png)

När du har aktiverat den här inställningen skapas en ny tjänstidentitet i din Azure Active Directory (Azure AD) och konfigureras till programtjänstvärden.

Tilldela nu den här tjänstidentiteten till en roll i det nödvändiga omfånget i servicebussresurserna.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Så här tilldelar du RBAC-roller med Azure-portalen
Om du vill tilldela en roll till ett servicebussnamnområde navigerar du till namnområdet i Azure-portalen. Visa inställningarna för åtkomstkontroll (IAM) för resursen och följ dessa instruktioner för att hantera rolltilldelningar:

> [!NOTE]
> I följande steg tilldelas en tjänstidentitetsroll till dina servicebussnamnområden. Du kan följa samma steg för att tilldela en roll i andra scope som stöds (resursgrupp och prenumeration). 
> 
> [Skapa ett service bus messaging-namnområde](service-bus-create-namespace-portal.md) om du inte har något. 

1. I Azure-portalen navigerar du till tjänstbussens namnområde och visar **översikten** för namnområdet. 
1. Välj **Åtkomstkontroll (IAM)** på den vänstra menyn för att visa åtkomstkontrollinställningar för servicebussens namnområde.
1.  Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar.
3.  Välj **Lägg till** om du vill lägga till en ny roll.
4.  På sidan **Lägg till rolltilldelning** väljer du de Azure Service Bus-roller som du vill tilldela. Sök sedan för att hitta tjänstidentiteten som du hade registrerat för att tilldela rollen.
    
    ![Lägg till sidan för rolltilldelning](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Välj **Spara**. Identiteten som du har tilldelat rollen visas under den rollen. Följande avbildning visar till exempel att tjänstidentitet har Azure Service Bus Data ägare.
    
    ![Identitet som tilldelats en roll](./media/service-bus-managed-service-identity/role-assigned.png)

När du har tilldelat rollen har webbprogrammet åtkomst till Service Bus-entiteterna under det definierade scopet. 

### <a name="run-the-app"></a>Kör appen

Nu ändrar du standardsidan för det ASP.NET program som du har skapat. Du kan använda webbprogramkoden från [den här GitHub-databasen](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Sidan Default.aspx är din målsida. Koden finns i Default.aspx.cs filen. Resultatet är ett minimalt webbprogram med några inmatningsfält och med knappar för **att skicka** och **ta emot** som ansluter till Service Bus för att antingen skicka eller ta emot meddelanden.

Observera hur [MessagingFactory-objektet](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) initieras. I stället för att använda SAS-tokenleverantören (Shared Access Token) skapar koden `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` en tokenprovider för den hanterade identiteten med anropet. Som sådan finns det inga hemligheter att behålla och använda. Flödet av den hanterade identitetskontexten till Service Bus och auktoriseringshandskakningen hanteras automatiskt av tokenprovidern. Det är en enklare modell än att använda SAS.

När du har gjort dessa ändringar publicerar och kör du programmet. Du kan enkelt hämta rätt publiceringsdata genom att hämta och sedan importera en publiceringsprofil i Visual Studio:

![Få publicera profil](./media/service-bus-managed-service-identity/msi3.png)
 
Om du vill skicka eller ta emot meddelanden anger du namnet på namnområdet och namnet på den entitet som du skapade. Klicka sedan på **skicka** eller **ta emot**.


> [!NOTE]
> - Den hanterade identiteten fungerar bara i Azure-miljön, på App-tjänster, virtuella Azure-datorer och skalningsuppsättningar. För .NET-program tillhandahåller Microsoft.Azure.Services.AppAuthentication-biblioteket, som används av Service Bus NuGet-paketet, en abstraktion över det här protokollet och stöder en lokal utvecklingsupplevelse. Med det här biblioteket kan du också testa koden lokalt på utvecklingsmaskinen med hjälp av ditt användarkonto från Visual Studio, Azure CLI 2.0 eller Active Directory Integrated Authentication. Mer information om lokala utvecklingsalternativ med det här biblioteket finns i Autentisering från [Tjänst till tjänst till Azure Key Vault med .NET](../key-vault/service-to-service-authentication.md).  
> 
> - För närvarande fungerar hanterade identiteter inte med distributionsplatser för App Service.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
