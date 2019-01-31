---
title: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure AD Graph API
description: En självstudie som steg för steg beskriver hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure AD Graph API.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: priyamo
ms.openlocfilehash: ad2d20bc54a2de5a049beadac779fff47d39d9b4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167962"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Självstudier: Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Azure AD Graph API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Den här självstudien visar hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure AD Graph API för att hämta dess gruppmedlemskap. Hanterade identiteter för Azure-resurser hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden.  

I den här självstudien kör du frågor om medlemskapet för din virtuella dators identitet i Azure AD-grupper. Gruppinformation används ofta i auktoriseringsbeslut. Under ytan representeras den virtuella datorns identitet av **tjänstens huvudnamn** i Azure AD. 

> [!div class="checklist"]
> * Anslut till Azure AD
> * Lägga till den virtuella datorns identitet i Azure AD 
> * Bevilja den virtuella datorns identitet åtkomst till Azure AD Graph 
> * Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet och använda den för att anropa Azure AD Graph

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Om du vill bevilja en virtuell dators identitet åtkomst till Azure AD Graph måste ditt konto tilldelas rollen **Global administratör** i Azure AD.

## <a name="connect-to-azure-ad"></a>Anslut till Azure AD

Du måste ansluta till Azure AD för att tilldela den virtuella datorn till en grupp samt ge den virtuella datorn behörighet att hämta dess gruppmedlemskap.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Lägga till den virtuella datorns identitet i Azure AD

När du aktiverade systemtilldelad identitet på den virtuella Linux-datorn skapades ett tjänsthuvudnamn i Azure AD.  Du måste lägga till den virtuella datorn i en grupp. I följande artikel finns instruktioner för hur du lägger till en virtuell dator i en grupp i Azure AD:

- [Lägga till gruppmedlemmar](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Bevilja den virtuella datorn åtkomst till Azure AD Graph API

Med hjälp av hanterade identiteter för Azure-resurser kan din kod hämta åtkomsttoken för att autentisera mot resurser som har stöd för Azure AD-autentisering. Microsoft Azure AD Graph API stöder Azure AD-autentisering. I det här steget beviljar du den virtuell datorns tjänsthuvudnamn åtkomst till Azure AD Graph så att den kan köra frågor om gruppmedlemskap. Tjänsthuvudnamn beviljas åtkomst till Microsoft eller Azure AD Graph via **Programbehörigheter**. Den typ av programbehörighet du behöver tilldela beror på vilken entitet du vill ha åtkomst till i MS eller Azure AD Graph.

I den här självstudien beviljar du den virtuella datorns identitet möjligheten att köra frågor om gruppmedlemskap med programbehörigheten `Directory.Read.All`. Om du vill tilldela den här behörigheten måste du ha ett användarkonto som har tilldelats rollen Global administratör i Azure AD. Normalt skulle du tilldela en programbehörighet genom att gå till programmets registrering i Azure-portalen och lägga till behörigheten där. Men hanterade identiteter för Azure-resurser registrerar inte programobjekt i Azure AD, utan registrerar bara tjänsthuvudnamn. Du registrerar programbehörigheten med hjälp av kommandoradsverktyget Azure AD PowerShell. 

Azure AD Graph:
- Tjänsthuvudnamnets appId (används vid tilldelning av programbehörighet): 00000002-0000-0000-c000-000000000000
- Resurs-ID (används vid begäran av åtkomsttoken från hanterade identiteter för Azure-resurser): https://graph.windows.net
- Referens för behörighetsomfång: [Referens för Azure AD Graph-behörigheter](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Bevilja programbehörigheter med CURL

1. Hämta en token för att göra CURL-begäranden:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Du måste hämta och anteckna `objectId` för den virtuella datorn. Det används i efterföljande steg för att bevilja behörigheter för den virtuella datorn att läsa dess gruppmedlemskap. Ersätt `<ACCESS TOKEN>` med åtkomsttoken du har hämtat i föregående steg.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Med hjälp av Azure AD Graph appID, 00000002-0000-0000-c000-000000000000, hämtar och noterar du `objectId` för `odata.type: Microsoft.DirectoryServices.ServicePrincipal` och `id` för approllbehörigheten `Directory.Read.All`.  Ersätt `<ACCESS TOKEN>` med den åtkomsttoken som du hämtade tidigare.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Svar:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Ge nu den virtuella datorns tjänsthuvudnamn läsåtkomst till Azure AD-katalogobjekt med hjälp av Azure AD Graph API.  Värdet `id` är värdet för approllbehörigheten `Directory.Read.All` och `resourceId` är `objectId` för tjänstens huvudnamn `odata.type:Microsoft.DirectoryServices.ServicePrincipal` (värdena du antecknade i det föregående steget).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns identitet för att anropa Azure AD Graph 

Om du vill slutföra de här stegen behöver du en SSH-klient. Om du använder Windows kan du använda SSH-klienten i [Windows-undersystemet för Linux](https://msdn.microsoft.com/commandline/wsl/about). Om du behöver hjälp att konfigurera SSH-klientens nycklar läser du [Använda SSH-nycklar med Windows i Azure](../../virtual-machines/linux/ssh-from-windows.md) eller [Så här skapar du säkert ett offentligt och ett privat SSH-nyckelpar för virtuella Linux-datorer i Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. I portalen går du till den virtuella Linux-datorn och i **översikten** klickar du på **Anslut**.  
2. **Anslut** till den virtuella datorn med valfri SSH-klient. 
3. Använd CURL i terminalfönstret och skicka en begäran till de lokala hanterade identiteterna för Azure-resursslutpunkter för att hämta en åtkomsttoken för Azure AD Graph.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   Svaret innehåller den åtkomsttoken som du behöver för att komma åt Azure AD Graph.

   Svar:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. Med hjälp av objekt-ID:t för den virtuella datorns tjänsthuvudnamn (värdet du hämtade i tidigare steg) kan du fråga Azure AD Graph API för att hämta dess gruppmedlemskap. Ersätt `<OBJECT-ID>` med objekt-ID:t för den virtuella datorns tjänsthuvudnamn och `<ACCESS-TOKEN>` med den tidigare hämtade åtkomsttoken:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Svar:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder en systemtilldelad hanterad identitet för en virtuell Linux-dator för att få åtkomst till Azure AD Graph.  Mer information om Azure AD Graph finns i:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
