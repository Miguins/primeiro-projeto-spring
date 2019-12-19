[![Build Status](https://travis-ci.org/Miguins/primeiro-projeto-spring.svg?branch=master)](https://travis-ci.org/Miguins/primeiro-projeto-spring)

# primeiro-projeto-spring

# Notas para o Spring Boot 2
O curso foi criado utilizando o Spring 1.5.x, porém ao executar o próximo passo você verá que o Spring Boot 2 foi lançado e pode ser selecionado para a criação do projeto.

O Spring Boot 2 é um major release, ou seja, possui novas funcionalidade e algumas diferenças em relação as versões anteriores, que fazem alguns dos componentes aqui ensinados não funcionarem sem alguns pequenos ajustes.

Caso você deseje utilizar o Spring Boot 2 para acompanhar o curso, segue a lista de alterações que deverão ser feitas para que tudo funcione corretamente.

Recomendo você anotar as diferenças a seguir, e fazer tais alterações conforme os tópicos forem apresentados durante as aulas.

1. Modificando o Flyway e o Hibernate

No curso ao executar os testes desabilitamos o Flyway para que as entidades sejam geradas diretamente pelo Hibernate no banco H2, que executa em memória, então no arquivo application-test.properties você deverá alterar da seguinte forma:

Spring 1.5.x:

flyway.enabled=false
Spring 2:

spring.flyway.enabled=false
Já para o Spring Boot 2, devemos adicionar uma configuração adicional ao Hibernate no arquivo application.properties:

spring.jpa.properties.hibernate.id.new_generator_mappings=false
2. Na nova versão do JPA, devemos substituir os comandos findOne e delete por findById e deleteById, respectivamente. Seguem exemplos de como era e como ficará:

Spring 1.5.x:

return Optional.ofNullable(this.lancamentoRepository.findOne(id));
this.lancamentoRepository.delete(id);
Spring 2:

//Repare que no findById não precisamos do Optional pois ele já retorna por padrão assim
return this.lancamentoRepository.findById(id);
this.lancamentoRepository.deleteById(id);
Assim quando for implementar as classes de serviços da API, sempre faça essa alteração quando um do dois métodos forem utilizados.

3. O Spring Security também demanda de uma pequena alteração, sendo assim, quando você estiver criando o arquivo WebSecurityConfig.java, adicione o seguinte método nessa classe para criar corretamente o anthenticationManager:

@Bean(name = BeanIds.AUTHENTICATION_MANAGER)
@Override
public AuthenticationManager authenticationManagerBean() throws Exception {
   return super.authenticationManagerBean();
}

4. Atualizar as dependências de validação dos DTOs (opcional):

Na versão 1.5.x do Spring Boot, quando criamos os arquivos DTOs aplicamos algumas anotações de validação, que são a @Email e @NotEmpty, importamos essas duas anotações de:

import org.hibernate.validator.constraints.Email;
import org.hibernate.validator.constraints.NotEmpty;
Essas anotações foram marcadas como deprecated no Spring Boot 2.x.x, portanto embora elas ainda continuem funcionando, elas serão removidas em versões futuras, o que nos faz não utilizá-las o quanto antes, as substituindo por suas sucessoras.

Portanto quando você tentar importar essas anotações do pacote acima, a IDE lhe informará que elas não devem ser utilizadas, e elas deverão ser importadas de:

import javax.validation.constraints.Email;
import javax.validation.constraints.NotEmpty;
Pronto, seguindo esses passos a sua API será 100% compatível com a mais recente versão do Spring Boot 2!


=======================================================================================================================


# Nota para a aula seguinte sobre o TravisCI
Na aula a seguir configuraremos o TravisCI, e você notará que criaremos um novo arquivo em nosso projeto, o .travis.yml.



1. Atualize a versão da JDK

Devido a alterações na licença da JDK da Oracle, o TravisCI passou a gerar um erro ao tentarmos ulitizá-la. Para solucionar esse problema, ao invés de utilizar a JDK oraclejdk8, utilize a openjdk8, que é uma JDK alternativa e gratuita para a oficial da Oracle.

Seu arquivo a ser criado na aula a seguir deverá ficar assim:

language: java
jdk:
  - openjdk8


2. Solucionando um possível problema de permissão de arquivo

Alguns alunos utilizando o Windows reportaram um erro ao executar o TravisCI com a configuração do arquivo que utilizamos no vídeo, portanto caso você ao executar os passos da aula seguinte receba o seguinte erro ao realizar o build permission denied, modifique o seu arquivo .travis.yml para a seguinte configuração para solucionar o problema:

language: java
jdk:
  - openjdk8
before_install:
  - chmod +x mvnw