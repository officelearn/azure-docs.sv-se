#### <a name="create-an-a-record-set-with-single-record"></a>Skapa en A-postuppsättning med en post

Använd `azure network dns record-set create` för att skapa en postuppsättning. Ange resursgrupp, zonnamn, relativt namn för postuppsättningen, posttyp och Time to Live (TTL).

```azurecli
    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

När du har skapat A-postuppsättningen så lägger du till IPv4-adressen till posten med `azure network dns record-set add-record`.

```azurecli
    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Skapa en AAAA-postuppsättning med en post

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"
```

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Skapa en CNAME-postuppsättning med en post

CNAME-poster tillåter bara ett enda strängvärde.

```azurecli
    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"
```

#### <a name="create-an-mx-record-set-with-a-single-record"></a>Skapa en MX-postuppsättning med en post

I det här exemplet använder vi postuppsättningsnamnet "@" för att skapa MX-posten i basdomänen (i det här fallet "contoso.com"). Detta är vanligt för MX-poster.

```azurecli
    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5
```

#### <a name="create-an-ns-record-set-with-a-single-record"></a>Skapa en NS-postuppsättning med en post

```azurecli
    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Skapa en PTR-postuppsättning med en post

I det här fallet står ”my-arpa-zone.com” för ARPA-zonen som representerar ditt IP-adressintervall.  Varje PTR-post som har angetts i den här zonen motsvarar en IP-adress i IP-intervallet.

```azurecli
    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Skapa en SRV-postuppsättning med en post

Om du skapar en SRV-post i zonens rot så kan du ange "_service" och "_protocol" i postnamnet. "@" behöver inte inkluderas i postnamnet.

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"
```

#### <a name="create-a-txt-record-set-with-single-record"></a>Skapa en TXT-postuppsättning med en post

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
```


<!--HONumber=Nov16_HO2-->


