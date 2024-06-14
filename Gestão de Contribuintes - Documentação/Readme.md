# Gestão de Contribuintes - Banco de Dados
Este documento descreve a estrutura e a configuração do banco de dados utilizado na aplicação "Gestão de Contribuintes". Este banco de dados armazena informações sobre contribuintes, incluindo casos de multiparentalidade onde é possível cadastrar mais de um CPF relacionado a um segundo pai ou mãe.

# Tecnologias Utilizadas
- PostgreSql
- Docker

# Estrutura do Banco de Dados

O banco de dados utiliza o esquema contribuintes para organizar suas tabelas.

# Tabelas
### Contribuintes
A tabela *contribuintes* armazena informações detalhadas sobre os contribuintes.

```
CREATE TABLE IF NOT EXISTS contribuintes.contribuintes (
    cpf VARCHAR(11) PRIMARY KEY,
    nome_civil VARCHAR(100),
    nome_social VARCHAR(100),
    endereco VARCHAR(255),
    email VARCHAR(100),
    salario NUMERIC(10,2),
    categoria VARCHAR(50),
    telefone VARCHAR(20),
    inicio_contribuicao DATE,
    tipo_relacionamento VARCHAR(50),
    cpf_pai VARCHAR(11),
    cpf_mae VARCHAR(11),
    cpf_pai2 VARCHAR(11),
    cpf_mae2 VARCHAR(11),
    cpf_conjuge VARCHAR(11)
);
```
- contribuintes.contribuintes: Nome da tabela.
- cpf VARCHAR(11) PRIMARY KEY: CPF do contribuinte (chave primária).
- nome_civil VARCHAR(100): Nome civil do contribuinte.
- nome_social VARCHAR(100): Nome social do contribuinte.
- endereco VARCHAR(255): Endereço do contribuinte.
- email VARCHAR(100): Email do contribuinte.
- salario NUMERIC(10,2): Salário do contribuinte.
- categoria VARCHAR(50): Categoria do contribuinte (ex.: empregado, autônomo).
- telefone VARCHAR(20): Telefone do contribuinte.
- inicio_contribuicao DATE: Data de início da contribuição.
- tipo_relacionamento VARCHAR(50): Tipo de relacionamento do contribuinte.
- cpf_pai VARCHAR(11): CPF do pai do contribuinte.
- cpf_mae VARCHAR(11): CPF da mãe do contribuinte.
- cpf_pai2 VARCHAR(11): CPF do segundo pai do contribuinte (se aplicável).
- cpf_mae2 VARCHAR(11): CPF da segunda mãe do contribuinte (se aplicável).
- cpf_conjuge VARCHAR(11): CPF do cônjuge do contribuinte.


### Dependentes
A tabela *dependentes* armazena informações sobre os dependentes dos contribuintes.
```
CREATE TABLE IF NOT EXISTS contribuintes.dependentes (
    cpf VARCHAR(11) PRIMARY KEY,
    nome VARCHAR(100)
);
```
- contribuintes.dependentes: Nome da tabela.
- cpf VARCHAR(11) PRIMARY KEY: CPF do dependente (chave primária).
- nome VARCHAR(100): Nome do dependente.


### Contribuintes_Dependentes
A tabela *contribuintes_dependentes* gerencia a relação entre contribuintes e seus dependentes.

```
CREATE TABLE IF NOT EXISTS contribuintes.contribuintes_dependentes (
    cpf_contribuinte VARCHAR(11),
    cpf_dependente VARCHAR(11),
    PRIMARY KEY (cpf_contribuinte, cpf_dependente),
    FOREIGN KEY (cpf_contribuinte) REFERENCES contribuintes.contribuintes(cpf),
    FOREIGN KEY (cpf_dependente) REFERENCES contribuintes.dependentes(cpf)
);
```
- contribuintes.contribuintes_dependentes: Nome da tabela.
- cpf_contribuinte VARCHAR(11): CPF do contribuinte.
- cpf_dependente VARCHAR(11): CPF do dependente.
- PRIMARY KEY (cpf_contribuinte, cpf_dependente): Chave primária composta.
- FOREIGN KEY (cpf_contribuinte) REFERENCES contribuintes.contribuintes(cpf): Chave estrangeira para contribuintes.
- FOREIGN KEY (cpf_dependente) REFERENCES contribuintes.dependentes(cpf): Chave estrangeira para dependentes.

### Contribuintes_Conjuges
A tabela *contribuintes_conjuges* gerencia a relação entre contribuintes e seus cônjuges.

```
CREATE TABLE IF NOT EXISTS contribuintes.contribuintes_conjuges (
    cpf_contribuinte VARCHAR(11),
    cpf_conjuge VARCHAR(11),
    PRIMARY KEY (cpf_contribuinte, cpf_conjuge),
    FOREIGN KEY (cpf_contribuinte) REFERENCES contribuintes.contribuintes(cpf),
    FOREIGN KEY (cpf_conjuge) REFERENCES contribuintes.contribuintes(cpf)
);
```

- contribuintes.contribuintes_conjuges: Nome da tabela.
- cpf_contribuinte VARCHAR(11): CPF do contribuinte.
- cpf_conjuge VARCHAR(11): CPF do cônjuge do contribuinte.
- PRIMARY KEY (cpf_contribuinte, cpf_conjuge): Chave primária composta.
- FOREIGN KEY (cpf_contribuinte) REFERENCES contribuintes.
- contribuintes(cpf): Chave estrangeira para contribuintes.
- FOREIGN KEY (cpf_conjuge) REFERENCES contribuintes.contribuintes(cpf): Chave estrangeira para contribuintes.

# Relacionamento Lógico

### Contribuintes e Dependentes
A relação entre contribuintes e seus dependentes é gerenciada pela tabela contribuintes_dependentes. Cada registro nessa tabela representa uma ligação entre um contribuinte e um dependente, assegurando que todos os CPFs referenciados existam nas tabelas correspondentes (contribuintes e dependentes).

### Contribuintes e Cônjuges
A relação entre contribuintes e seus cônjuges é gerenciada pela tabela contribuintes_conjuges. Cada registro nessa tabela representa uma ligação entre dois contribuintes como cônjuges, garantindo que ambos os CPFs existam na tabela contribuintes.

Esta estrutura robusta permite à aplicação Gestão de Contribuintes gerenciar de maneira eficaz e organizada os dados relacionados aos contribuintes, seus dependentes e cônjuges, mantendo a integridade e a consistência dos dados.
