---
title: "Min första Python-runbook i Azure Automation | Microsoft Docs"
description: "Självstudiekurs som vägleder dig genom att skapa, testa och publicering av en enkel Python-runbook."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: gwallace
ms.openlocfilehash: 0399719e5f80869f0131691b89a9dc48b3fc48d7
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="my-first-python-runbook"></a>Min första Python-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Den här självstudiekursen vägleder dig genom att skapa en [Python runbook](automation-runbook-types.md#python-runbooks) i Azure Automation. Vi börjar med en enkel runbook som vi testar och publicerar medan vi förklarar hur du spårar statusen för runbook-jobbet. Sedan ändrar vi vår runbook så att den hanterar Azure-resurser, i vårt exempel ska den starta en virtuell dator i Azure. Slutligen ska vi göra runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom vi ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="create-a-new-runbook"></a>Skapa en ny runbook

Vi börjar med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.
   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).
1. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.
   ![RunbooksControl](media/automation-first-runbook-textual-python/runbooks-control-tiles.png)
1. Skapa en ny runbook genom att klicka på knappen **Lägg till en runbook** och sedan på **Skapa en ny runbook**.
1. Ge runbook namnet *MyFirstRunbook Python*.
1. I det här fallet är det dags att skapa en [Python runbook](automation-runbook-types.md#python-runbooks) så väljer **Python 2** för **runbooktyp**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="add-code-to-the-runbook"></a>Lägg till kod i runbook

Nu vi lägga till ett enkelt kommando för att skriva ut texten ”Hello World”:

```python
print("Hello World!")
```

Klicka på **spara** att spara runbook.

## <a name="test-the-runbook"></a>Testa runbook

Innan vi publicerar runbooken så att den blir tillgänglig i produktionsmiljön vill vi testa den och kontrollera att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
   ![Testfönster](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-test.png)
1. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.
   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.
1. När runbook-jobbet är klart visas dess utdata. I detta fall bör du se *Hello World*.
1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbook

Den runbook som vi skapade är fortfarande i utkastläge. Vi måste publicera den innan vi kan köra den i produktion.
När du publicerar en runbook kan du skriva över den befintliga publicerade versionen med utkastet.
I vårt fall har vi inte en publicerad version ännu eftersom vi just skapade runbook.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
   ![Knappen Publicera](media/automation-first-runbook-textual-python/automation-runbook-edit-controls-publish.png)
1. Om du rullar åt vänster för att visa runbooken i fönstret **Runbooks** visas den nu med **redigeringsstatusen** **Publicerad**.
1. Bläddra till höger för att visa fönstret för **MyFirstRunbook Python**.
   Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
1. Eftersom vi vill starta runbooken klickar vi på **Starta** och sedan på **OK** när bladet Starta runbook öppnas.
1. Ett jobbfönster öppnas för runbook-jobbet som vi skapade. Vi kan stänga det här fönstret, men ska i stället lämna det öppet så att vi kan titta på jobbets status.
1. Jobbets status visas i **Jobbsammanfattning** och överensstämmer med de statusar som vi såg när vi testade runbooken.
1. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret Utdata öppnas och *Hello World* visas.
1. Stäng utdatafönstret.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Vi bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbooken skriver till dem.
1. Stäng fönstret dataströmmar och fönstret jobb att återgå till fönstret MyFirstRunbook Python.
1. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Vi bör endast se ett jobb i listan eftersom vi bara körde jobbet en gång.
1. Du kan klicka på det här jobbet för att öppna samma jobbfönster som vi visade när vi startade runbooken. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägg till autentisering för att hantera Azure-resurser

Vi har testat och publicerat vår runbook, men hittills gör den egentligen inget användbart. Vi vill att den ska hantera Azure-resurser.
Om du vill hantera Azure-resurser skriptet måste autentisera med autentiseringsuppgifter från din [Automation-konto](automation-offering-get-started.md).

> [!NOTE]
> Automation-kontot måste ha skapats med funktionen tjänstens huvudnamn att ett runas-certifikat.
> Om ditt automation-konto inte har skapats med tjänstens huvudnamn, du kan autentisera med den metod som beskrivs i [autentisera med Azure Management-bibliotek för Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Öppna Redigeraren för textrepresentation genom att klicka på **redigera** i rutan MyFirstRunbook Python.
1. Lägg till följande kod för att autentisera till Azure:
   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Lägg till kod för att skapa Python Compute-klienten och starta den virtuella datorn

Om du vill arbeta med virtuella Azure-datorer, skapa en instans av den [Azure Compute-klienten för Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Använd beräknings-klienten för att starta den virtuella datorn. Lägg till följande kod i runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Där _MyResourceGroup_ är namnet på resursgruppen som innehåller den virtuella datorn och _TestVM_ är namnet på den virtuella datorn som du vill starta. 

Testa och köra runbook igen för att se att den startar den virtuella datorn.

## <a name="use-input-parameters"></a>Använd indataparametrar

Hårdkodade värden används för närvarande för namnen på resursgruppen och den virtuella datorn.
Nu ska vi lägga till kod som hämtar värdena från indataparametrar.

Vi använder den `sys.argv` variabeln för att hämta parametervärden.
Lägg till följande kod i runbook omedelbart efter det andra `import` instruktioner:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Detta importerar det `sys` modulen, och skapar två variabler för att lagra namnen på resursgruppen och VM.
Lägg märke till att elementet i argumentlistan `sys.argv[0]`är namnet på skriptet och inte är indata av användaren.

Vi kan nu ändra de två sista raderna för runbook och Använd Indataparametern värdena i stället för hårdkodade värden:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

När du startar en Python-runbook (antingen på den **Test** bladet eller som en publicerad runbook), kan du ange värden för parametrarna i den **starta Runbook** bladet under **parametrar**.

![Parametern värderutan](media/automation-first-runbook-textual-python/runbook-python-param-highlight.png)

När du har startat om du anger ett värde i den första rutan en andra visas, och så vidare, så att du kan ange så många parametervärden som behövs.

Värdena är tillgängliga med skriptet som den `sys.argv` matris som koden som vi just lade till.

Ange namnet på resursgruppen som värde för den första parametern och namnet på den virtuella datorn att starta som värde för den andra parametern.

![Ange parametervärden](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klicka på **OK** att starta runbook. Runbook körs och startar den virtuella datorn som du angav.

## <a name="next-steps"></a>Nästa steg

* Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Läs om hur du utvecklar för Azure med Python i [Azure för Python-utvecklare](https://docs.microsoft.com/python/azure/?view=azure-python).
* Runbook-exempel Python 2 finns i [Azure Automation GitHub](https://docs.microsoft.com/python/azure/?view=azure-python).
