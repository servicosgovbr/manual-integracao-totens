Primeiros passos
================

Contato
-------

Para realizar a integração com o orquestrador de digitais é necessário preencher o Plano de Integração, e enviar para o e-mail: 
``integracao-totens-govbr@gestao.gov.br``

`Baixar Plano de Integração`_

O e-mail deve seguir o padrão:

 - Assunto:

    - Integração com Totem

 - Conteúdo:

    - Nome do Órgão

    - CNPJ

    - E-mail institucional

    - Nome do sistema Integrado

    - IPs de saída para internet da rede dos Totens

    - Plano de Integração Preenchido

.. _`Baixar Plano de Integração`: https://github.com/servicosgovbr/manual-integracao-totens/raw/refs/heads/main/arquivos/PlanoIntegracaoTotem.docx

Contexto de integração com o gov.br
===================================

A aplicação que se integrar com o orquestrador de digitais do gov.br será executada em um terminal de autoatendimento físico que será chamado daqui em diante por **'totem'**.

No totem haverá um site cliente do gov.br. Nessa integração o gov.br disponibilizará a opção de se autenticar nesse site por meio de impressão digital.

.. image:: _images/login.png
   :height: 300px
   :align: center

O cidadão ao selecionar a opção de **Login com digital** será redirecionado para o orquestrador de digitais do gov.br devendo informar o seu CPF.

.. image:: _images/cpf.png
   :height: 300px
   :align: center

O gov.br valida o CPF e redireciona para a aplicação integrada ao orquestrador. A aplicação realiza a leitura física da impressão digital do cidadão.

Uma vez concluída a leitura, o cidadão é redirecionado de volta para o orquestrador e em seguida é autenticado no site pelo gov.br.

Cidadãos autenticados por meio de impressão digital ganham confiabilidade de nível ouro na conta gov.br.