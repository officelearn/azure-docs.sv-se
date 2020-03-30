---
title: Felreferens för hälsokontroller
description: Felkoder och möjliga lösningar på problem som hittades genom att köra diagnostikkommandot az acr check-health i Azure Container Registry
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289149"
---
# <a name="health-check-error-reference"></a>Referens för felkontroll vid hälsokontroll

Följande är information om felkoder som returneras av [az acr check-health][az-acr-check-health] kommandot. För varje fel visas möjliga lösningar.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Det här felet innebär att Docker-klienten för CLI inte kunde hittas. Därför körs inte följande ytterligare kontroller: hitta Docker-versionen, utvärdera Docker-demonstatus och köra ett Docker pull-kommando.

*Potentiella lösningar*: Install Docker-klient; lägga till Docker-sökväg till systemvariablerna.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Det här felet innebär att docker-demonstatusen inte är tillgänglig eller att den inte kunde nås med CLI.This error means that the Docker daemon status is unavailable, or that it could not be reached using the CLI. Därför är Docker-åtgärder (till exempel `docker login` och `docker pull`) inte tillgängliga via CLI.

*Potentiella lösningar*: Starta docker-demon eller verifiera att den är korrekt installerad.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Det här felet innebär att CLI `docker --version`inte kunde köra kommandot .

*Potentiella lösningar*: Prova att köra kommandot manuellt, se till att du har den senaste CLI-versionen och undersöka felmeddelandet.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Det här felet innebär att CLI inte kunde dra en exempelavbildning till din miljö.

*Potentiella lösningar*: Verifiera att alla komponenter som behövs för att hämta en bild körs korrekt.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Detta fel innebär att Helm-klienten inte kunde hittas av CLI, vilket utesluter andra Helm-åtgärder.

*Potentiella lösningar*: Kontrollera att Helm-klienten är installerad och att dess sökväg läggs till i systemmiljövariablerna.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Det här felet innebär att CLI inte kunde fastställa Helm-versionen installerad. Detta kan inträffa om Azure CLI-versionen (eller om Helm-versionen) som används är föråldrad.

*Potentiella lösningar*: Uppdatera till den senaste Azure CLI-versionen eller till den rekommenderade Helm-versionen. köra kommandot manuellt och undersöka felmeddelandet.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Det här felet innebär att DNS för den angivna registerinloggningsservern pingades men svarade inte, vilket innebär att den inte är tillgänglig. Detta kan tyda på vissa anslutningsproblem. Alternativt kanske registret inte finns, användaren kanske inte har behörigheterna för registret (för att hämta sin inloggningsserver korrekt) eller så finns målregistret i ett annat moln än det som används i Azure CLI.

*Potentiella lösningar*: Validera anslutning; Kontrollera stavningen av registret och registret finns, kontrollera att användaren har rätt behörighet på den och att registrets moln är detsamma som används i Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Det här felet innebär att utmaningsslutpunkten för det angivna registret svarade med en 403 Förbjuden HTTP-status. Det här felet innebär att användarna inte har åtkomst till registret, troligen på grund av en virtuell nätverkskonfiguration. Om du vill se de `az acr show --query networkRuleSet --name <registry>`för närvarande konfigurerade brandväggsreglerna kör du .

*Potentiella lösningar*: Ta bort virtuella nätverksregler eller lägg till den aktuella klient-IP-adressen i den tillåtna listan.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Det här felet innebär att målregistrets utmaningsslutpunkt inte ifrågasatte en utmaning.

*Potentiella lösningar*: Försök igen efter en tid. Om felet kvarstår öppnar du https://aka.ms/acr/issuesett problem på .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Det här felet innebär att utmaningsslutpunkten för målregistret utfärdade en utmaning, men registret stöder inte Azure Active Directory-autentisering.

*Potentiella lösningar*: Prova på ett annat sätt att autentisera, till exempel med administratörsautentiseringsuppgifter. Om användare behöver autentisera med Azure Active https://aka.ms/acr/issuesDirectory öppnar du ett problem på .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Det här felet innebär att registerinloggningsservern inte svarade med en uppdateringstoken, så åtkomst till målregistret nekades. Det här felet kan uppstå om användaren inte har rätt behörighet för registret eller om användarautentiseringsuppgifterna för Azure CLI är inaktuella.

*Potentiella lösningar*: Kontrollera om användaren har rätt behörighet i registret. för `az login` att uppdatera behörigheter, token och autentiseringsuppgifter.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Det här felet innebär att registerinloggningsservern inte svarade med en åtkomsttoken, så att åtkomsten till målregistret nekades. Det här felet kan uppstå om användaren inte har rätt behörighet för registret eller om användarautentiseringsuppgifterna för Azure CLI är inaktuella.

*Potentiella lösningar*: Kontrollera om användaren har rätt behörighet i registret. för `az login` att uppdatera behörigheter, token och autentiseringsuppgifter.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Det här felet innebär att klienten inte kunde upprätta en säker anslutning till behållarregistret. Det här felet uppstår vanligtvis om du kör eller använder en proxyserver.

*Potentiella lösningar:* Mer information om att arbeta bakom en proxy [finns här](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy).

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Det här felet innebär att CLI inte kunde hitta inloggningsservern för det angivna registret och inget standardsuffix hittades för det aktuella molnet. Det här felet kan uppstå om registret inte finns, om användaren inte har rätt behörighet i registret, om registrets moln och det aktuella Azure CLI-molnet inte matchar eller om Azure CLI-versionen är föråldrad.

*Potentiella lösningar*: Kontrollera att stavningen är korrekt och att registret finns. kontrollera att användaren har rätt behörighet i registret och att molnen i registret och CLI-miljön matchar; uppdatera Azure CLI till den senaste versionen.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Det här felet innebär att CLI inte är kompatibel med den installerade versionen av Docker/Notary. Prova att nedgradera din notary.exe-version till en version tidigare än 0.6.0 genom att ersätta Notarie-adrollsklienten för Docker manuellt för att lösa problemet.

## <a name="next-steps"></a>Nästa steg

Alternativ för att kontrollera hälsotillståndet för ett register finns [i Kontrollera hälsotillståndet för ett Azure-behållarregister](container-registry-check-health.md).

Se [vanliga frågor](container-registry-faq.md) och svar om vanliga frågor och andra kända problem om Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
