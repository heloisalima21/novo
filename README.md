 """
Turma - 93313
 
Nome dos componentes.
1 - Heloisa Lima
2 - Júlia Vitória
3 - Fábio de Carvalho
 """

import os
from sqlalchemy import create_engine, Column, String, Integer, Float
from sqlalchemy.orm import sessionmaker, declarative_base

# Criando banco de dados.
MEU_BANCO = create_engine("sqlite:///meubanco.db", echo=True)  # echo=True para logs SQL

# Criando conexão com banco de dados.
Session = sessionmaker(bind=MEU_BANCO)
session = Session()

# Definindo a base para as classes de banco de dados.
Base = declarative_base()

# Criando a tabela de Funcionário (Usuario)
class Funcionario(Base):
    __tablename__ = 'funcionarios'

    id = Column(Integer, primary_key=True)
    nome = Column(String)
    email = Column(String)
    senha = Column(String)
    idade = Column(Integer)
    cpf = Column(String)
    setor = Column(String)
    funcao = Column(String)
    salario = Column(Float)
    telefone = Column(String)

    def __repr__(self):
        return f"<Funcionario(id={self.id}, nome={self.nome}, email={self.email})>"

# Criando tabela no banco de dados (caso ainda não exista).
Base.metadata.create_all(bind=MEU_BANCO)

# Função para salvar um funcionário no banco de dados
def salvar_funcionario(funcionario):
    """
    Adiciona um novo funcionário ao banco de dados.
    :param funcionario: Dicionário com dados do funcionário
    """
    novo_funcionario = Funcionario(
        nome=funcionario['nome'],
        email=funcionario['email'],
        senha=funcionario['senha'],
        idade=funcionario['idade'],
        cpf=funcionario['cpf'],
        setor=funcionario['setor'],
        funcao=funcionario['funcao'],
        salario=funcionario['salario'],
        telefone=funcionario['telefone']
    )
    session.add(novo_funcionario)
    session.commit()
    print(f"Funcionário {funcionario['nome']} salvo com sucesso!")

# Função para listar todos os funcionários
def listar_todos_funcionarios():
    """
    Exibe todos os funcionários cadastrados no banco de dados.
    """
    funcionarios = session.query(Funcionario).all()
    if not funcionarios:
        print("Não há funcionários cadastrados.")
    else:
        for funcionario in funcionarios:
            print(funcionario)

# Função para pesquisar um funcionário pelo ID
def pesquisar_um_funcionario(id_funcionario):
    """
    Pesquisa um funcionário pelo ID no banco de dados.
    :param id_funcionario: ID do funcionário
    """
    funcionario = session.query(Funcionario).filter_by(id=id_funcionario).first()
    return funcionario

# Função para atualizar dados de um funcionário
def atualizar_funcionario(id_funcionario, novos_dados):
    """
    Atualiza os dados de um funcionário específico.
    :param id_funcionario: ID do funcionário
    :param novos_dados: Dicionário com os novos dados
    """
    funcionario = pesquisar_um_funcionario(id_funcionario)
    if funcionario:
        for chave, valor in novos_dados.items():
            setattr(funcionario, chave, valor)
        session.commit()
        print(f"Funcionário {id_funcionario} atualizado com sucesso!")
    else:
        print(f"Funcionário com ID {id_funcionario} não encontrado.")

# Função para excluir um funcionário pelo ID
def excluir_funcionario(id_funcionario):
    """
    Exclui um funcionário pelo ID.
    :param id_funcionario: ID do funcionário
    """
    funcionario = pesquisar_um_funcionario(id_funcionario)
    if funcionario:
        session.delete(funcionario)
        session.commit()
        print(f"Funcionário com ID {id_funcionario} excluído com sucesso!")
    else:
        print(f"Funcionário com ID {id_funcionario} não encontrado.")

# Salvar no banco de dados.
os.system("cls || clear")  # Limpa a tela

# Solicitar dados para adicionar um novo funcionário
print("Solicitando dados para adicionar funcionário")
inserir_nome = input("Digite seu nome: ")
inserir_email = input("Digite seu e-mail: ")
inserir_senha = input("Digite sua senha: ")
inserir_idade = int(input("Digite sua idade: "))
inserir_cpf = input("Digite seu CPF: ")
inserir_setor = input("Digite seu setor: ")
inserir_funcao = input("Digite sua função: ")
inserir_salario = float(input("Digite seu salário: "))
inserir_telefone = input("Digite seu telefone: ")

# Criando um dicionário com os dados para salvar
dados_funcionario = {
    'nome': inserir_nome,
    'email': inserir_email,
    'senha': inserir_senha,
    'idade': inserir_idade,
    'cpf': inserir_cpf,
    'setor': inserir_setor,
    'funcao': inserir_funcao,
    'salario': inserir_salario,
    'telefone': inserir_telefone
}

# Chamando a função para salvar no banco
salvar_funcionario(dados_funcionario)

# Listando todos os funcionários cadastrados no banco de dados
print("\nExibindo todos os funcionários cadastrados no banco de dados.")
listar_todos_funcionarios()

# Excluir um funcionário
print("\nExcluindo um funcionário.")
id_excluir = int(input("Informe o ID do funcionário para excluir: "))
excluir_funcionario(id_excluir)

# Atualizar dados de um funcionário
print("\nAtualizando dados de um funcionário.")
id_atualizar = int(input("Informe o ID do funcionário a ser atualizado: "))
novos_dados = {
    'nome': input("Novo nome: "),
    'email': input("Novo e-mail: "),
    'senha': input("Nova senha: ")
}
atualizar_funcionario(id_atualizar, novos_dados)

# Listando todos os funcionários novamente após as alterações
print("\nExibindo todos os funcionários após as alterações.")
listar_todos_funcionarios()

# Fechando a conexão com o banco de dados
session.close()
