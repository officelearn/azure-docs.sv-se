---
title: Så här skapar du och signerar en Azure-attesterings princip
description: En förklaring av hur du skapar och signerar en attesterings princip.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c8ffdcd0615913649e80b20f6873d005f4ad4410
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675985"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Så här skapar och signerar du en policy för attestering

Attesterings principen är en fil som laddats upp för att Microsoft Azure attestering. Azure-attestering erbjuder flexibiliteten att ladda upp en princip i ett beskrivande princip format. Du kan också ladda upp en kodad version av principen i JSON-webbsignaturen. Princip administratören ansvarar för att skriva policyn för attestering. I de flesta attesterings scenarier fungerar den förlitande parten som princip administratör. Klienten som gör attesterings anropet skickar bevis för attestering, som tjänsten parsar och konverterar till inkommande anspråk (uppsättning egenskaper, värde). Tjänsten bearbetar sedan anspråken baserat på vad som definieras i principen och returnerar det beräknade resultatet.

Principen innehåller regler som avgör auktoriseringsregler, egenskaper och innehållet i attesterings-token. En exempel princip fil ser ut så här:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
En princip fil har tre segment, som du ser ovan:

- **version** : versionen är versions numret för den grammatik som följs. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    För närvarande är den enda version som stöds version 1,0.

- **authorizationrules** : en samling anspråks regler som ska kontrol leras först, för att avgöra om Azure-attesteringen bör fortsätta till **issuancerules** . Anspråks reglerna gäller i den ordning som de definieras.

- **issuancerules** : en samling anspråks regler som kommer att utvärderas för att lägga till ytterligare information till attesterings resultatet enligt vad som definieras i principen. Anspråks reglerna gäller i den ordning som de är definierade och är också valfria.

Mer information finns i [anspråk och anspråks regler](claim-rule-grammar.md) .
   
## <a name="drafting-the-policy-file"></a>Utkast till princip filen

1. Skapa en ny fil.
1. Lägg till version i filen.
1. Lägg till avsnitt för **authorizationrules** och **issuancerules** .

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Auktoriseringsregler innehåller åtgärden Deny () utan något villkor för att se till att inga regler för utfärdande har bearbetats. Dessutom kan auktoriseringsregeln även innehålla åtgärden Tillåt () för att tillåta bearbetning av utgivnings regler.
  
4. Lägg till anspråks regler i auktoriseringsregler

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Om den inkommande anspråks uppsättningen innehåller ett anspråk som matchar typ, värde och utfärdare, kommer åtgärden Tillåt () att instruera princip motorn att bearbeta **issuancerules** .
  
5. Lägg till anspråks regler i **issuancerules** .

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Den utgående anspråks uppsättningen kommer att innehålla ett anspråk med:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Komplexa principer kan användas på samma sätt. Mer information finns i [exempel på attesterings principer](policy-examples.md).
  
6. Spara filen.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Skapa princip filen i JSON Web Signature-format

När du har skapat en princip fil, för att överföra en princip i JWS-format, följer du stegen nedan.

1. Generera JWS, RFC 7515 med princip (UTF-8-kodad) som nytto lasten
     - Nytto Last-ID: t för den Base64Url kodade principen ska vara "AttestationPolicy".
     
     Exempel-JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Valfritt Signera principen. Azure-attestering stöder följande algoritmer:
     - **Ingen** : Signera inte princip nytto lasten.
     - **RS256** : algoritm som stöds för att signera principens nytto Last

3. Ladda upp JWS och validera principen.
     - Om princip filen är fri från syntaxfel godkänns princip filen av tjänsten.
     - Om princip filen innehåller syntaxfel avvisas princip filen av tjänsten.

## <a name="signing-the-policy"></a>Principen signeras

Nedan visas ett exempel på Python-skript för hur du utför princip signerings åtgärd

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Nästa steg
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
- [Intyga en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)