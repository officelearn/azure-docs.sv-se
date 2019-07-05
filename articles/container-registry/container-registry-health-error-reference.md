---
title: Error reference for health check - Azure Container Registry
description: Felkoder och möjliga lösningar på problem som hittas genom att köra az acr-hälsostatus diagnostiska kommando i Azure Container Registry
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: fc29b27cbb7eea983140c59529d981ad95c27ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555116"
---
# <a name="health-check-error-reference"></a>Hälsotillstånd Kontrollera felreferens

Nedan visas information om felkoder som returneras av den [az acr-hälsostatus][az-acr-check-health] kommando. Möjliga lösningar visas för varje fel.

## <a name="dockercommanderror"></a>DOCKER_COMMAND_ERROR

Detta fel innebär att Docker-klienten för CLI inte kunde hittas. Därför kan följande ytterligare kontroller körs inte: söka efter Docker-version, utvärderar Docker daemon status och kör en Docker pull-kommando.

*Möjliga lösningar*: Installera Docker-klienten. Lägg till Docker-sökvägen i systemvariablerna.

## <a name="dockerdaemonerror"></a>DOCKER_DAEMON_ERROR

Detta fel innebär att Docker-daemon status är otillgänglig eller att det inte gick att nå med hjälp av CLI. Resultatet blir Docker-åtgärder (till exempel `docker login` och `docker pull`) är inte tillgängliga via CLI.

*Möjliga lösningar*: Starta om Docker-daemon eller verifiera att den är korrekt installerat.

## <a name="dockerversionerror"></a>DOCKER_VERSION_ERROR

Detta fel innebär att CLI går inte att köra kommandot `docker --version`.

*Möjliga lösningar*: Försök köra kommandot manuellt, kontrollera att du har den senaste versionen av CLI och undersök felmeddelandet.

## <a name="dockerpullerror"></a>DOCKER_PULL_ERROR

Detta fel innebär att CLI inte kunde hämta en exempelbild för din miljö.

*Möjliga lösningar*: Verifiera att alla komponenter som behövs för att hämta en avbildning körs korrekt.

## <a name="helmcommanderror"></a>HELM_COMMAND_ERROR

Detta fel innebär att Helm-klienten inte kunde hittas av CLI, vilket utesluter andra Helm-åtgärder.

*Möjliga lösningar*: Kontrollera att Helm-klienten är installerad och att sökvägen har lagts till systemets miljövariabler.

## <a name="helmversionerror"></a>HELM_VERSION_ERROR

Detta fel innebär att CLI inte gick att fastställa Helm-version som installeras. Detta kan inträffa om Azure CLI-version (eller om Helm-version) som används är föråldrad.

*Möjliga lösningar*: Uppdatera till den senaste versionen av Azure CLI eller till den rekommenderade Helm-versionen. Kör kommandot manuellt och undersök felmeddelandet.

## <a name="connectivitydnserror"></a>CONNECTIVITY_DNS_ERROR

Det här felet innebär att DNS för angivna behållarregistrets inloggningsserver har en men inte svarade, vilket innebär att den inte är tillgänglig. Detta kan tyda på vissa problem med nätverksanslutningen. Du kan också registret finns inte, användaren kanske inte har behörighet för registret (för att hämta dess inloggningsserver korrekt) eller målregistret är i ett annat moln än den som används i Azure CLI.

*Möjliga lösningar*: Kontrollera anslutning; Kontrollera stavningen av registret och att registret finns. Kontrollera att användaren har rätt behörigheter på den och att registrets molnet är samma som används i Azure CLI.

## <a name="connectivityforbiddenerror"></a>CONNECTIVITY_FORBIDDEN_ERROR

Detta fel innebär att utmaning slutpunkten för den angivna registernyckeln svarade med en 403 tillåts inte HTTP-status. Detta fel innebär att användare inte har åtkomst till registret, troligen på grund av en konfiguration av virtuellt nätverk.

*Möjliga lösningar*: Ta bort virtuella Nätverksregler eller lägga till den aktuella klientens IP-adressen i listan över tillåtna.

## <a name="connectivitychallengeerror"></a>CONNECTIVITY_CHALLENGE_ERROR

Detta fel innebär att utmaning slutpunkten för målregistret inte har utfärdat en utmaning.

*Möjliga lösningar*: Försök igen om en stund. Om felet kvarstår, öppna ett ärende i https://aka.ms/acr/issues.

## <a name="connectivityaadloginerror"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Detta fel innebär att utmaning slutpunkten för målregistret utfärdat en utmaning, men registret har inte stöd för Azure Active Directory-autentisering.

*Möjliga lösningar*: Prova ett annat sätt att autentisera, till exempel med autentiseringsuppgifter som administratör. Om användare måste autentisera med Azure Active Directory, öppna ett ärende i https://aka.ms/acr/issues.

## <a name="connectivityrefreshtokenerror"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Detta fel innebär att behållarregistrets inloggningsserver inte svarade med en uppdateringstoken så nekas åtkomst till målregistret. Det här felet kan inträffa om användaren inte har rätt behörigheter på registret eller om användarens autentiseringsuppgifter för Azure CLI är inaktuella.

*Möjliga lösningar*: Kontrollera om användaren har rätt behörigheter för registret. kör `az login` att uppdatera behörigheter, token och autentiseringsuppgifter.

## <a name="connectivityaccesstokenerror"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Detta fel innebär att behållarregistrets inloggningsserver inte svarade med en åtkomst-token så att åtkomsten till målregistret nekades. Det här felet kan inträffa om användaren inte har rätt behörigheter på registret eller om användarens autentiseringsuppgifter för Azure CLI är inaktuella.

*Möjliga lösningar*: Kontrollera om användaren har rätt behörigheter för registret. kör `az login` att uppdatera behörigheter, token och autentiseringsuppgifter.

## <a name="loginservererror"></a>LOGIN_SERVER_ERROR

Det här felet innebär att CLI inte gick att hitta inloggningsserver på det angivna registret och inga standardsuffixet hittades för det aktuella molnet. Det här felet kan inträffa om registret inte finns, om användaren inte har rätt behörigheter på registret om registrets molnet och det aktuella Azure CLI-molnet inte matchar, eller om Azure CLI version är föråldrad.

*Möjliga lösningar*: Kontrollera att stavningen är korrekt och att registret finns; Kontrollera som användaren har rätt behörigheter för registret och som matchar moln på registret och CLI-miljö; Uppdatera Azure CLI till den senaste versionen.

## <a name="next-steps"></a>Nästa steg

Alternativ för att kontrollera hälsotillståndet för ett register, se [kontrollerar hälsotillståndet för ett Azure container registry](container-registry-check-health.md).

Se den [vanliga frågor och svar](container-registry-faq.md) för vanliga frågor och svar och andra kända problem med Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
