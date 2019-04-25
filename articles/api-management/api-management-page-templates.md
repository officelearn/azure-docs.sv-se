---
title: Sidan mallar i Azure API Management | Microsoft Docs
description: Lär dig mer om att anpassa innehållet på utvecklarportalens sidor med en uppsättning mallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1fbafcdab938a0f8653df48631d7733cc58a3668
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441918"
---
# <a name="page-templates-in-azure-api-management"></a>Mallar i Azure API Management
Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalens sidor med en uppsättning mallar som konfigurerar sitt innehåll. Med hjälp av [DotLiquid](http://dotliquidmarkup.org/) syntax och redigeringsprogram, till exempel [DotLiquid för Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), och en angiven uppsättning lokaliserade [sträng resurser](api-management-template-resources.md#strings), [tecken resurser](api-management-template-resources.md#glyphs), och [sidan kontroller](api-management-page-controls.md), har bra möjlighet att konfigurera innehållet i sidorna som du vill med hjälp av dessa mallar.  
  
 Mallar i det här avsnittet kan du anpassa innehållet på Logga in, logga in och sidan att hitta inte sidor i developer-portalen.  
  
-   [Logga in](#SignIn)  
  
-   [Registrera dig](#SignUp)  
  
-   [Det gick inte att hitta sidan](#PageNotFound)  
  
> [!NOTE]
>  Standardmallarna för exemplet ingår i följande dokumentation, men kan komma att ändras på grund av fortlöpande med förbättringar. Du kan visa live standardmallarna i developer-portalen genom att navigera till önskad enskilda mallarna. Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a> Logga in  
 Den **logga in** mall kan du anpassa inloggningssidan i developer-portalen.  
  
 ![Inloggningssidan](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM inloggning sidan Utvecklarportalsmallar")  
  
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
 Den här mallen kan använda följande [sidan kontroller](api-management-page-controls.md).  
  
-   [Basic-inloggning](api-management-page-controls.md#basic-signin)  
  
-   [Providers](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Datamodell  
 [Användarinloggning i](api-management-template-data-model-reference.md#UseSignIn) entitet.  
  
### <a name="sample-template-data"></a>Mall för exempeldata  
  
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
  
##  <a name="SignUp"></a> Registrera sig  
 Den **registrera** mall kan du anpassa sidan registrering i developer-portalen.  
  
 ![Registrera dig sidan](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "sidan Utvecklarportalsmallar APIM-registrering")  
  
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
 Den här mallen kan använda följande [sidan kontroller](api-management-page-controls.md).  
  
-   [Registrera dig](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datamodell  
 [Användarregistrering](api-management-template-data-model-reference.md#UserSignUp) entitet.  
  
### <a name="sample-template-data"></a>Mall för exempeldata  
  
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
  
##  <a name="PageNotFound"></a> Det gick inte att hitta sidan  
 Den **sidan inte att hitta** mallen kan du anpassa sidan inte att hitta sidan i developer-portalen.  
  
 ![Gick inte att hitta sidan](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM gick inte att hitta sidan Developer Portal mallar")  
  
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
 Den här mallen kan inte använda någon [sidan kontroller](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|referenceCode|sträng|Kod som genereras om den här sidan visades som ett resultat av ett internt fel.|  
|errorCode|sträng|Kod som genereras om den här sidan visades som ett resultat av ett internt fel.|  
|emailBody|sträng|E-brödtext som genereras om den här sidan visades som ett resultat av ett internt fel.|  
|requestedUrl|sträng|Den Webbadress som efterfrågas när sidan inte hittades.|  
|referrerUrl|sträng|Den begärda Webbadressen referent URL.|  
  
### <a name="sample-template-data"></a>Mall för exempeldata  
  
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
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).
