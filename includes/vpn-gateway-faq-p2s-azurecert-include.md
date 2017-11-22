[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Kan jag använda min egen interna PKI-rotcertifikatutfärdare för punkt-till-plats-anslutningen?

Ja. Tidigare kunde bara självsignerade rotcertifikat användas. Du kan fortfarande överföra 20 rotcertifikat.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Vilka verktyg kan jag använda för att skapa certifikat?

Du kan använda din Företags-PKI-lösning (ett internt PKI), Azure PowerShell, MakeCert och OpenSSL.

### <a name="certsettings"></a>Finns det anvisningar för inställningar för certifikat och parametrar?

* **Intern PKI/Enterprise PKI-lösning:** Se stegen för att [generera certifikat](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** finns i [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) artikel anvisningar.

* **MakeCert:** finns i [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) artikel anvisningar.

* **OpenSSL:** 

    * När du exporterar certifikat, bör du konvertera rotcertifikatet till Base64.

    * För klientcertifikat:

      * När du skapar den privata nyckeln måste du ange hur länge som 4096.
      * När du skapar certifikatet för den *-tillägg* parameter, ange *usr_cert*.