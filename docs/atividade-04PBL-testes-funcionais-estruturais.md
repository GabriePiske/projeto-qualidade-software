#Testes Funcionais e Estruturais

Equipe: Tech Quality

 Integrante: Gabriel Piske

 Sistema: Local Eats (https://local-eats-unisenac.vercel.app/static/index.html)


#1. Funcionalidade

 🔹Funcionalidade selecionada:
  
    - Busca de Restaurantes
  
 🔹Descrição da funcionalidade:

    - Permite que o usuário pesquise restaurantes com base em filtros como tipo de culinária, localização e faixa de preço. 
  
 🔹O que o usuário espera:

    - O usuário espera encontrar resultados corretos, rápidos e relevantes de acordo com os filtros informados.


#2. Testes de Caixa-preta (visão do usuario)

  
🔹Cenários de teste
   
   Cenário 1:

     - Buscar por tipo de culinária (ex: “pizza”) → sistema deve retornar restaurantes desse tipo
   
   Cenário 2:

     - Buscar com múltiplos filtros (ex: “pizza + barato + perto”) → resultados devem respeitar todos os filtros

   Cenário 3:

     - Buscar algo inexistente → sistema deve informar que não há resultados

   Cenário 4: 
  
     - Buscar sem preencher filtros → sistema deve mostrar sugestões ou lista geral
     
   
  🔹Possiveis erros identificados
     
     - Resultados incorretos ou irrelevantes
     - Filtros não funcionando corretamente
     - Nenhum resultado sendo exibido mesmo com dados existentes
     - Lentidão na resposta da busca 


#3. Testes de Caixa-branca (visão do sistema)

  🔹 Lógica hipotética (pseudo-código ou descrição)

    <!-- se (filtro preenchido) então
    
    aplicar filtro de tipo
    
    aplicar filtro de localização
    
    aplicar filtro de preço
    
    fim

    buscar dados no banco

    se (resultado vazio) então
    retornar mensagem "nenhum resultado encontrado"
    
    senão
    exibir lista de restaurantes
    
    fim -->

  
 🔹Situações a serem testadas 
       
    - Situação 1: filtros combinados corretamente (AND / OR)
    - Situação 2: validação de campos vazios
    - Situação 3: tratamento de erro na consulta ao banco

  
 🔹Possiveis erros identificados
     
     - Erro na lógica de combinação de filtros
     - Consulta incorreta ao banco de dados
     - Falha ao tratar resultados vazios
     - Problemas de desempenho na consulta 


#4. Comparação entre as abordagens


  🔹 Testes caixa-preta focam no comportamento do sistema do ponto de vista do usuário, enquanto testes caixa-branca analisam a  lógica interna e implementação do código.

  🔹Caixa-preta
     
     - Erros de funcionalidade, problemas de usabilidade e resultados incorretos. 

  🔹Caixa-preta
     
     - Erros na lógica do código, falhas em condições, problemas internos e caminhos não testados.   


#5. Reflexão no contexto do LocalEats

   

 🔹Qual abordagem parece mais importante neste momento do projeto?

    - A abordagem caixa-preta é mais importante inicialmente, pois o sistema já está em produção e apresenta problemas visíveis para o usuário.

 🔹Apenas uma abordagem seria suficiente? Por quê?
 
    - Não. As duas abordagens são necessárias, pois a caixa-preta identifica problemas visíveis ao usuário e a caixa-branca ajuda a encontrar a causa desses problemas no código.

#Conclusão

    - A equipe compreendeu que testes caixa-preta e caixa-branca são complementares. Enquanto um foca na experiência do usuário, o outro analisa a estrutura interna do sistema.
