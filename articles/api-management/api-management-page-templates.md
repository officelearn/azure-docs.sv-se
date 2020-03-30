---
title: Sidmallar i Azure API Management | Microsoft-dokument
description: Lär dig hur du anpassar innehållet på utvecklarportalsidor med hjälp av en uppsättning mallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ce56c406c884471c445b25343d5c42f9edcbe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249547"
---
# <a name="page-templates-in-azure-api-management"></a>Sidmallar i Azure API Management
Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalsidor med hjälp av en uppsättning mallar som konfigurerar deras innehåll. Med hjälp av [DotLiquid-syntax](http://dotliquidmarkup.org/) och valfri redigerare, till exempel [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)och en uppsättning lokaliserade [strängresurser,](api-management-template-resources.md#strings) [Glyph-resurser](api-management-template-resources.md#glyphs)och [sidkontroller,](api-management-page-controls.md)har du stor flexibilitet att konfigurera innehållet på sidorna som du tycker passar med hjälp av dessa mallar.  
  
 Med mallarna i det här avsnittet kan du anpassa innehållet i inloggningen, registrera och sidan som inte finns på sidorna i utvecklarportalen.  
  
-   [Logga in](#SignIn)  
  
-   [Registrera sig](#SignUp)  
  
-   [Sidan hittades inte](#PageNotFound)  
  
> [!NOTE]
>  Exempelmallar för exempel ingår i följande dokumentation, men kan komma att ändras på grund av kontinuerliga förbättringar. Du kan visa standardmallarna i utvecklarportalen genom att navigera till önskade enskilda mallar. Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)för API Management med hjälp av mallar .  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a>Logga in  
 Med **sign in** inloggningsmallen kan du anpassa inloggningssidan i utvecklarportalen.  
  
 ![Inloggningssida](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Portalmallar för APIM-inloggningssida")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Kontroller  
 Den här mallen kan använda följande [sidkontroller](api-management-page-controls.md).  
  
-   [grundläggande signin](api-management-page-controls.md#basic-signin)  
  
-   [Leverantörer](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Datamodell  
 [Användaren loggar in](api-management-template-data-model-reference.md#UseSignIn) entitet.  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="sign-up"></a><a name="SignUp"></a>Registrera sig  
 Med **sign up** registreringsmallen kan du anpassa registreringssidan i utvecklarportalen.  
  
 ![Registrera dig sida](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "Portalmallar för anmälningssida för APIM-registrering")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Kontroller  
 Den här mallen kan använda följande [sidkontroller](api-management-page-controls.md).  
  
-   [Registrera dig](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datamodell  
 [Användaren registrerar](api-management-template-data-model-reference.md#UserSignUp) entitet.  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a>Sidan hittades inte  
 Med **mallen hittades inte kan** du anpassa sidan som inte finns på utvecklarportalen.  
  
 ![Sidan hittades inte](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM hittades inte sida developer portal mallar")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Kontroller  
 Den här mallen får inte använda några [sidkontroller](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|referensKod|sträng|Kod som genereras om den här sidan visades som ett resultat av ett internt fel.|  
|errorCode|sträng|Kod som genereras om den här sidan visades som ett resultat av ett internt fel.|  
|e-postKropp|sträng|E-posttext som genereras om den här sidan visades som ett resultat av ett internt fel.|  
|requestedUrl|sträng|Webbadressen som begärdes när sidan inte hittades.|  
|hänvisarreUrl|sträng|Hänvisningsadressen till den begärda webbadressen.|  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .
