# Consulta NF-e <span id="trialSpan"></span>

API para acesso a Notas Fiscais Eletrônicas diretamente das bases da Receita Federal do Brasil através de sua chave de acesso.

A plataforma APIGOV (Plataforma que contempla todas as API's disponibilizadas e comercializadas pelo SERPRO) utiliza o protocolo Oauth2 - Client Credential Grant ([https://tools.ietf.org/html/rfc6749#section-4.4](https://tools.ietf.org/html/rfc6749#section-4.4)) para realizar a autenticação e autorização de acesso para consumo das API's contratadas, conforme figura abaixo:

<img title="Processo de autenticação e autorização APIS" src="https://raw.githubusercontent.com/devserpro/consulta-cpf/master/img/oauth.png" style="width=50%;" />

## Como fazer consultas a Consulta NF-e

Para consumir a Consulta NF-e, você deverá utilizar os dois códigos (Consumer Key e Consumer Secret) disponibilizados na Área do Cliente. Esses códigos servem para identificar o contrato e deverão ser informados sempre que uma consulta for realizada.
Exemplos de códigos:

**Consumer Key**: djaR21PGoYp1iyK2n2ACOH9REdUb

**Consumer Secret**: ObRsAJWOL4fv2Tp27D1vd8fB3Ote

### 1 – Como solicitar o Token de Acesso (Bearer)
Para consultar a API, é necessário obter um token de acesso temporário (Bearer). Esse token possui um tempo de validade e sempre que expirado, este passo de requisição de um novo token de acesso deve ser repetido. 

Para solicitar o token temporário é necessário realizar uma requisição HTTP POST para o endpoint Token https://apigateway.serpro.gov.br/token, informando as credenciais de acesso(consumerKey:consumerSecret) no HTTP Header Authorization, no formato base64, conforme exemplo abaixo. As credenciais de acesso devem ser obtidas a partir do portal do cliente Serpro - https://minhaconta.serpro.gov.br

```
[POST] grant_type=client_credentials
[HEAD] Authorization: Basic base64(Consumer Key:Consumer Secret)
```

Abaixo segue um exemplo de chamada via cUrl:

```curl
curl -k -d "grant_type=client_credentials" -H "Authorization: Basic ZGphUjIxUEdvWXAxaXlLMm4yQUNPSDlSRWRVYjpPYlJzQUpXT0w0ZnYyVHAyN0QxdmQ4ZkIzT3RlCg" https://apigateway.serpro.gov.br/token
```

A chave informada no exemplo acima "ZGphUjIxUEdvWXAxaXlLMm4yQUNPSD
lSRWRVYjpPYlJzQUpXT0w0ZnYyVHAyN0QxdmQ4ZkIzT3RlCg" é resultado do BASE64 dos códigos Consumer Key e Consumer Secret separados pelo caracter “:”, conforme exemplo a seguir:

```curl
echo "djaR21PGoYp1iyK2n2ACOH9REdUb:ObRsAJWOL4fv2Tp27D1vd8fB3Ote" | base64
```

**Receba o Token**

Como resultado, o endpoint informará o token de acesso a API, no campo access_token da mensagem json de retorno. Este token deve ser informado nos próximos passos.

```json
{"scope":"am_application_scope default","token_type":"Bearer","expires_in":3295,"access_token":"c66a7def1c96f7008a0c397dc588b6d7"}
```

**Renovação do Token de Acesso**

Atentar que sempre que o token de acesso temporário expirar, o gateway vai retornar um _HTTP CODE 401_ após realizar uma requisição para uma API. Neste caso, deve ser repetido o passo anterior (**Como solicitar o Token de Acesso (Bearer)**) para geração de um novo token de acesso temporário.


### 2 – Como realizar a consulta à API

De posse do Token de Acesso, faça uma requisição via GET ao gateway informando os parâmetros da API. Exemplo:

```curlBearer
curl -X GET --header "Accept: application/json" --header "Authorization: Bearer c66a7de41c96f7008a0c397dc588b6d7" "https://apigateway.serpro.gov.br/nfe/v1/12345678912345678912345678912345678912345678"
```

No exemplo acima foram utilizados os seguintes parametros:

**[HEADER] Accept: application/json** - Informamos o tipo de dados que estamos requerendo, nesse caso JSON

**[HEADER] Authorization: Bearer <span class="bearer">c66a7de41c96f7008a0c397dc588b6d7</span>** - Informamos o token de acesso recebido

**[GET] https://apigateway.serpro.gov.br/nfe<span id="trialSpanUrl"></span>/<span id="trialSpanVersao"></span>/12345678912345678912345678912345678912345678**: chamamos a url da API informando a chave de acesso da Nota Fiscal Eletrônica. No caso a url é "nfe<span id="trialSpanUrl2"></span>/<span id="trialSpanVersao2"></span>/{Chave de Acesso da NF-e}"

Nesse caso, espera-se que a resposta seja a seguinte:

```json
{
  "nfeProc": {
    "protNFe": {
      "infProt": {
        "nProt": 135170142171021,
        "dhRecbto": "2017-03-07T09:12:40-03:00",
        "Id": "Id135170142171071",
        "chNFe": "35170300776574000156550020009091941178378523",
        "xMotivo": "Autorizado o uso da NF-e",
        "cStat": 100
      }
    },
    "NFe": {
      "infNFe": {
        "infAdic": {
          "obsCont": [
            {
              "xCampo": "DevCO",
              "xTexto": "DEV"
            },
            {
              "xCampo": "SiglaERP",
              "xTexto": "ERP"
            },
            {
              "xCampo": "Logo",
              "xTexto": "LOGO"
            }
          ],
          "infCpl": "Desconto condicional concedido de        6.00 EC 87/2015 - TOTAL FECP: R$        0.00 TOTAL ICMS UF ORIGEM: R$        3.08 TOTAL ICMS UF DESTINO: R$        4.62"
        },
        "det": {
          "nItem": 1,
          "prod": {
            "cEAN": 12391112157934,
            "cProd": 123315383,
            "qCom": 1,
            "cEANTrib": 1891112157934,
            "vUnTrib": 59.99,
            "qTrib": 1,
            "vProd": 59.99,
            "nFCI": "46C5AB6C-F255-42EF-12AC-273260A2923D",
            "xProd": "JOGO FORMAS ALUMINIO 3 PCS",
            "vUnCom": 59.99,
            "indTot": 1,
            "uTrib": "PC",
            "NCM": 16151000,
            "uCom": "PC",
            "CFOP": 6108,
            "vFrete": 9.99
          },
          "imposto": {
            "vTotTrib": 24.38,
            "ICMS": {
              "ICMS00": {
                "modBC": 3,
                "orig": 0,
                "CST": "00",
                "vBC": 69.98,
                "vICMS": 4.9,
                "pICMS": 7
              }
            },
            "ICMSUFDest": {
              "pICMSInter": 7,
              "vICMSUFDest": 4.62,
              "vICMSUFRemet": 3.08,
              "pICMSInterPart": 60,
              "vFCPUFDest": 0,
              "pFCPUFDest": 0,
              "vBCUFDest": 69.98,
              "pICMSUFDest": 18
            },
            "COFINS": {
              "COFINSAliq": {
                "vCOFINS": 5.32,
                "CST": "01",
                "vBC": 69.98,
                "pCOFINS": 7.6
              }
            },
            "PIS": {
              "PISAliq": {
                "vPIS": 1.15,
                "CST": "01",
                "vBC": 69.98,
                "pPIS": 1.65
              }
            }
          }
        },
        "total": {
          "ICMSTot": {
            "vICMSUFDest": 4.62,
            "vICMSUFRemet": 3.08,
            "vCOFINS": 5.32,
            "vBCST": 0,
            "vICMSDeson": 0,
            "vFCPUFDest": 0,
            "vProd": 59.99,
            "vSeg": 0,
            "vNF": 69.98,
            "vTotTrib": 24.38,
            "vPIS": 1.15,
            "vBC": 69.98,
            "vST": 0,
            "vICMS": 4.9,
            "vII": 0,
            "vDesc": 0,
            "vOutro": 0,
            "vIPI": 0,
            "vFrete": 9.99
          }
        },
        "entrega": {
          "xCpl": "CASA",
          "UF": "DF",
          "CPF": "82771760070",
          "xLgr": "AVENIDA CENTRAL FICTICIA",
          "xMun": "BRASILIA",
          "nro": "08",
          "cMun": 5300108,
          "xBairro": "NUCLEO BANDEIRANTE"
        },
        "Id": "NFe35170300776574000156550020009091941178378523",
        "ide": {
          "tpNF": 1,
          "mod": 55,
          "indPres": 2,
          "tpImp": 1,
          "nNF": 129194,
          "cMunFG": 3534401,
          "procEmi": 0,
          "finNFe": 1,
          "dhEmi": "2017-03-07T09:12:22-03:00",
          "tpAmb": 1,
          "indFinal": 1,
          "dhSaiEnt": "2017-03-07T09:12:22-03:00",
          "idDest": 2,
          "tpEmis": 1,
          "cDV": 2,
          "cUF": 35,
          "serie": 2,
          "natOp": "Fat. Conta e Ordem N/Contrib",
          "cNF": 12337856,
          "verProc": "ELDOC",
          "indPag": 2
        },
        "emit": {
          "xNome": "Companhia Digital FICTICIA",
          "CRT": 3,
          "CNPJ": "57101771000192",
          "enderEmit": {
            "UF": "SP",
            "xPais": "BRASIL",
            "cPais": 1058,
            "xLgr": "Rua Ficticia",
            "xMun": "OSASCO",
            "nro": 123,
            "cMun": 3534401,
            "xBairro": "Presidente Ficticio",
            "CEP": "00000008"
          },
          "IE": 123413778117,
          "IEST": "1234443600757"
        },
        "dest": {
          "xNome": "Jorge Luiz Ficticio",
          "CPF": "14281245278",
          "enderDest": {
            "fone": 6155551454,
            "UF": "DF",
            "xPais": "Brasil",
            "cPais": 1058,
            "xLgr": "QUADRA ficticia",
            "xMun": "BRASILIA",
            "nro": 213,
            "cMun": 5300108,
            "xBairro": "SETOR DE HABITACOES FICTICIOS",
            "CEP": 71000000
          },
          "indIEDest": 9
        },
        "versao": 3.1,
        "transp": {
          "modFrete": 0,
          "vol": {
            "pesoL": 0.99,
            "esp": "VOLS",
            "qVol": 1,
            "pesoB": 0.99
          }
        }
      }
    },
    "versao": 3.1
  }
}
```

```trialSpanNFEs
Chaves de Acesso NF-e para ambiente TRIAL:

1) 35170608530528000184550000000154301000771561
2) 35161147508411011603551000077959551093041003
3) 53131205035672000156550010000004991543410167
4) 42140612687276000179550010001505671172228752
5) 35140903608306000202555000001100251571436929
6) 53150337992500000167550010000029521000029516
7) 31150917591579000107550010000014321081035918
8) 35151200991908000104550020000063951000184833
9) 31160906347409006953550110008369841081956475
10) 31170309339936000973550250002397736362483965
11) 35160900623904000335550010028792431858713854
12) 35170300776574000156550020009091941178378523

Atenção: As Chaves de Acesso acima foram geradas aleatoriamente contendo apenas dados fictícios.
```