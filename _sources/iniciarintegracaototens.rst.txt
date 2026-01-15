Integração com Totens
=====================

**Roteiro de Integração de Aplicação com Leitura de Impressão Digital com Orquestrador de Digitais do gov.br**

Um pouco sobre OAuth 2.0
------------------------

A aplicação integrada será o provedor (*authorization server*) e o orquestrador de digitais do gov.br será uma aplicação cliente desse provedor. 


Arquitetura da solução
----------------------

A arquitetura da solução é composta por três provedores OAuth 2.0 (`RFC 6749`_) envolvidos: 

- **OAuth gov.br**: aplicação cliente que irá ser autenticar no gov.br; 

- **OAuth orquestrador de digitais do gov.br**: aplicação que valida as digitais que serão capturadas; e  

- **OAuth aplicação integrada**:  Aplicação que será utilizada no terminal de autoatendimento e que realizara a leitura física da digital seguindo o fluxo Authorization Code Flow padrão do protocolo OAuth 2.0 (`RFC 6749`_).

.. _RFC 6749: https://datatracker.ietf.org/doc/html/rfc6749

.. image:: _images/oauth-orquestrador.png
   :align: center


Login do Totem
==============

**Integração do Orquestrador com o Login do Totem**

A aplicação integrada precisa fornecer as **credenciais** para os ambientes disponíveis. É necessário fornecer um **Client ID** e uma **Secret** para que sejam utilizados pelo orquestrador. Também é necessário cadastrar as **URIs de retorno**.

============ ========================================================================
**Ambiente** **URI de Retorno**                                                           
------------ ------------------------------------------------------------------------
Staging      https://digitais.staging.acesso.gov.br/redirection-endpoint/{PROVEDOR}  

Produção     https://digitais.acesso.gov.br/redirection-endpoint/{PROVEDOR}           
============ ========================================================================

O fluxo de autorização (*Authorization Code Flow*) consiste resumidamente em dois passos:

1. Authorization Endpoint
-------------------------

O orquestrador de digitais será o cliente da aplicação integrada. O orquestrador fará a requisição de autorização (*Authorization Endpoint*). Essa requisição é basicamente um redirecionamento do browser (HTTP 302) para o endpoint de autorização do provedor.

Exemplo de chamada à URI de autorização:

::

   302
   https://www.aplicacao-integrada.gov.br/authorize?response_type=code&scope=openid&redirect_uri=https://biometria.staging.acesso.gov.br/enter-account-id-redirection-endpoint/{PROVEDOR}&client_id=orquestrador-gov-br&state=MEU_STATE

Neste exemplo é passado um parâmetro query **'fingers'** com o valor  igual a 1 (indicador direito). Esse parâmetro mostra que praquele cpf utilizado no totem, a base do governo identificou que existe biometria pelo menos do dedo indicador direito, o que pode auxiliar a aplicação integrada a sugerir a leitura desse dedo em questão para validação da digital posteriormente. Esse parâmetro pode ser passado de forma opcional se a aplicação integrada preferir. 

Tabela de referência para os dedos:

==================== ==========
**Dedo**             **Valor**
-------------------- ----------
POLEGAR_DIREITO      0
INDICADOR_DIREITO    1
MEDIO_DIREITO        2
ANELAR_DIREITO       3
MINIMO_DIREITO       4
POLEGAR_ESQUERDO     5
INDICADOR_ESQUERDO   6
MEDIO_ESQUERDO       7
ANELAR_ESQUERDO      8
MINIMO_ESQUERDO      9
==================== ==========


Neste momento o usuário será redirecionado para a aplicação integrada, onde esta realizará o procedimento de leitura física da impressão digital.

Uma vez que o processo de leitura seja concluído, o provedor retornará para a URI de redirecionamento do orquestrador de digitais com o parâmetro **state**. Em caso de sucesso retornará também um parâmetro **code**, ou **error** caso tenha ocorrido algum erro.

Em caso de sucesso, o orquestrador realizará posteriormente uma chamada ao **Token Endpoint** no backend.

2. Token Endpoint
-----------------

Com o parâmetro **code** recebido, o orquestrador de digitais irá realizar uma chamada para o **Token Endpoint** definido pela aplicação integrada.

Exemplo:

::

   POST
   https://www.aplicacao-integrada.gov.br/token

   Headers:
   Authorization: Basic base64(client_id:secret)
   Content-Type: form-url-encoded

   grant_type=code
   client_id=orquestrador-gov-br
   redirect_uri=https://biometria.staging.acesso.gov.br/enter-account-id-redirection-endpoint/{PROVEDOR}
   code=CODE_RECEBIDO

A aplicação integrada pode disponibilizar a impressão digital lida por meio do **User Info Endpoint**. O resultado deve ser disponibilizado em formato JSON (`RFC 8259`_) contendo a impressão digital em formato `WSQ`_.

.. _RFC 8259: https://datatracker.ietf.org/doc/html/rfc8259

.. _WSQ: https://www.nist.gov/programs-projects/wsq-certification-procedure

Formato sugerido:

::

   {
       "polegar_direito": "POLEGAR_DIREITO_EM_WSQ"
   }

Uma vez validada a digital, o orquestrador retorna para que o usuário seja autenticado no gov.br.


Recuperação de conta
====================

O gov.br também disponibiliza um fluxo de recuperação de conta que pode ser realizado nos terminais de autoatendimento.

A aplicação integrada pode manter as **mesmas credenciais** utilizadas para o fluxo de **login**.

No fluxo de recuperação de contas no terminal de auto-atendimento o cidadão pode ter a necessidade de trocar a informação de contato (e-mail ou telefone) cadastrada no gov.br e validar esse novo contato. Nesse cenário ele pode precisar validar a sua digital mais de uma vez. Desta forma para o fluxo de recuperação de digitais é necessário cadastrar duas URIs de retorno por ambiente:



URLs de recuperação de conta em ambiente **staging**

================= =============================================================================================================================
**Função**        **URIs de Retorno**                                                                                         
----------------- -----------------------------------------------------------------------------------------------------------------------------
Alterar dados     https://biometria.staging.acesso.gov.br/enter-account-id-redirection-endpoint/{PROVEDOR} 
Validar dados     https://biometria.staging.acesso.gov.br/confirm-new-contact-redirection-endpoint/{PROVEDOR} 
================= =============================================================================================================================


URLs de recuperação de conta em ambiente **produção**

================= =============================================================================================================================
**Função**        **URIs de Retorno**                                                                                         
----------------- -----------------------------------------------------------------------------------------------------------------------------      
Alterar dados     https://biometria.acesso.gov.br/enter-account-id-redirection-endpoint/{PROVEDOR}         
Validar dados     https://biometria.acesso.gov.br/confirm-new-contact-redirection-endpoint/{PROVEDOR}
================= =============================================================================================================================

**Na tela principal de login não existe um link para a recuperação da conta por impressão digital. O link para esse fluxo é disponibilizado para cada aplicação integrada.**

Dúvidas?
========

Entre em contato conosco através do e-mail: ``integracao-totens-govbr@gestao.gov.br``
