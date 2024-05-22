const jwt = require('jsonwebtoken');
const axios = require('axios');
const qs = require('qs');

// Obs: Antes de realizar esse processo é necessário dar consentimento ao seu APP
// Primeiro passo e cadastrar uma URL de redirect para seu APP, no seu APP em Additional settings Redirect URIs, adicione http:localhost
// Segundo passo acessar URL logado na plataforma para dar consentimento ao seu APP, trocar o ${integrationId} pelo Id do seu APP
// URL -> https://account-d.docusign.com/oauth/auth?response_type=code&scope=signature%20impersonation&client_id=${integrationId}&redirect_uri=http:localhost
// para esse método de autenticação é necessário dar consentimento somente uma vez
// Essa URL esta com consentimento para o scope "signature impersonation", para outros tipos de autenticação tem que trocar o scope dar o consentimento novamente.


// URL da API do DocuSign
const baseUrl = 'https://demo.docusign.net/restapi/v2.1';
// URL de autenticação da DocuSign
const oauthToken = 'https://account-d.docusign.com/oauth/token';


// Dashboard => https://admindemo.docusign.com/apps-and-keys
const integrationId = '';
const userId = '';
const accountId = '';


//Dentro do App vc gera sua private key em "Service Integration"
const privateKey = ''; // chave privada

const createToken = () => {
    const token = jwt.sign(
        {
            iss: integrationId,
            sub: userId,
            aud: "account-d.docusign.com",
            scope: 'signature impersonation',
            exp: Math.floor(Date.now() / 1000) + 3600, // Expira em 1 hora
        },
        privateKey,
        { algorithm: 'RS256' }
    );

    return token;
}

const createAccessToken = async (token) => {

    const tokenData = qs.stringify({
        grant_type: 'urn:ietf:params:oauth:grant-type:jwt-bearer',
        assertion: token
    });

    const tokenHeaders = {
        'Content-Type': 'application/x-www-form-urlencoded'
    };

    const responseAccess = await axios.post(oauthToken, tokenData, { headers: tokenHeaders });
   return responseAccess.data.access_token;
}

const listTemplates = async (accessToken)=> {
    try {

        const response = await axios.get(`${baseUrl}/accounts/${accountId}/templates`, {
            headers: {
                Authorization: `Bearer ${accessToken}`,
            },
        });

        const templates = response.data.envelopeTemplates;
        console.log('Templates encontrados:');
        templates.forEach((template) => {
            console.log(`- Nome: ${template.name}, ID: ${template.templateId}`);
        });
    } catch (error) {
        console.error(error);
        console.error('Erro ao listar os templates:', error.message);
    }
}

const main = async ()=>{
    // Step - 1
    const token = createToken();

    // Step - 2
    const accessToken = await createAccessToken(token);

    // Step - 3
    await listTemplates(accessToken);
}

main();
