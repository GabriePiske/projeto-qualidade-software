#Estrategia inicial de testes - LocalEats

Equipe: Tech Quality

Integrante: Gabriel Piske

Sistema: Local Eats (https://local-eats-unisenac.vercel.app/static/index.html)



#1.Funcionalidades


    - Login

    - Busca de restaurantes
    
    - Visualização de restaurante (cardápio, avaliações)
    
    - Sistema de avaliações
    
    - Recomendações personalizadas


#2.Niveis de testes
 

  #Funcionalidade: Login

     - Unitário: validação de senha, e-mail e campos obrigatórios

     - Integração: comunicação com banco de dados e serviço de autenticação
     
     - Sistema: usuário realiza login completo
     
     - Aceitação: usuário consegue acessar sua conta sem erros  
  

  #Funcionalidade: Busca de restaurantes

     - Unitário: filtros de busca (tipo, localização, preço)

     - Integração: consulta ao banco de dados
     
     - Sistema: usuário realiza busca e recebe resultados
     
     - Aceitação: usuário encontra restaurantes corretamente


  #Funcionalidade: Visualização de restaurante

     - Unitário: carregamento de dados (nome, cardápio, avaliações)

     - Integração: integração com banco e APIs
     
     - Sistema: usuário acessa página do restaurante
     
     - Aceitação: usuário visualiza informações completas


  #Funcionalidade: Sistema de avaliações

     - Unitário: envio e validação de avaliação

     - Integração: salvar e recuperar avaliações
     
     - Sistema: usuário publica avaliação
     
     - Aceitação: avaliação aparece corretamente
  

  #Funcionalidade: Recomendações personalizadas

     - Unitário: lógica de recomendação

     - Integração: dados de usuário + histórico
     
     - Sistema: sistema sugere restaurantes
     
     - Aceitação: usuário recebe recomendações relevantes

#3. Prioridades e Riscos

  Alta prioridade:

    - Login → sem login o usuário não usa o sistema
    - Busca de restaurantes → funcionalidade principal
    - Sistema de avaliações → já apresenta falhas (dados desaparecendo)
    
    Justificativa: Falhas nessas funcionalidades impactam diretamente o uso do sistema e a confiança do usuário.

  Média prioridade:

    - Visualizão de restaurantes
    - Recomendações personalizadas
    
    Justificativa: elementos importantes pensando em experiencia mas não impedem o uso básico do sistema.


#4. Pirâmide de Testes
   
    - Maior foco: Testes unitários
    - Médio foco: Testes de integração
    - Menor foco: Testes de sistema e aceitação

    Justificativa: Testes unitários são mais rápidos e baratos, permitindo detectar erros cedo.
    Testes de integração garantem comunicação correta entre componentes.
    Testes de sistema são mais caros e lentos, então devem ser usados com menor frequência..


#5. Testes em Produção

    - Uso de monitoramento e testes controlados (ex: feature flags)
    - Aplicar em novas funcionalidades e recomendações

    Justificativa: Permite validar o comportamento real do sistema com usuários reais, identificando problemas que não aparecem em ambiente de teste..
