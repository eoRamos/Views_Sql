create table marcas (
mrc_id int auto_increment primary key,
mrc_nome varchar(50) not null,
mrc_nacionalidade varchar(50)

);

create table produtos (
prd_id int auto_increment primary key,
prd_nome varchar(50) not null,
prd_qtd_estoque int not null default(0),
prd_estoque_min int not null default(0),
prd_dat_fabricacao timestamp default now(),
prd_perecivel boolean,
prd_valor decimal(10,2),

prd_marcaid int,
constraint fk_marcas foreign key(prd_marca_id) references marcas (mrc_id)
);

create table fornecedores (
frn_id int auto_increment primary key,
frn_nome varchar(50) not null,
frn_email varchar(50)

);

create table produto_fornecedor (
pf_prod_id int references produtos (prd_id),
pf_forn_id int references fornecedores (frn_id),

primary key (pf_prod_id, pf_forn_id)
);

-- Cria a view que mostra todos os produtos e suas respectivas marcas
CREATE VIEW produtos_e_marcas AS
SELECT p.nome AS produto, m.nome AS marca
FROM produtos p
INNER JOIN marcas m ON p.marca_id = m.id;

-- Cria a view que mostra todos os produtos e seus respectivos fornecedores
CREATE VIEW produtos_e_fornecedores AS
SELECT p.nome AS produto, f.nome AS fornecedor
FROM produtos p
INNER JOIN fornecedores f ON p.fornecedor_id = f.id;

-- Cria a view que mostra todos os produtos, seus respectivos fornecedores e marcas
CREATE VIEW produtos_fornecedores_marcas AS
SELECT p.nome AS produto, f.nome AS fornecedor, m.nome AS marca
FROM produtos p
INNER JOIN fornecedores f ON p.fornecedor_id = f.id
INNER JOIN marcas m ON p.marca_id = m.id;

-- Cria a view que mostra todos os produtos com estoque abaixo do mínimo
CREATE VIEW produtos_com_estoque_baixo AS
SELECT *
FROM produtos
WHERE estoque < estoque_minimo;

-- Cria a view que mostra todos os produtos e suas respectivas marcas com validade vencida
CREATE VIEW produtos_com_validade_vencida AS
SELECT p.nome AS produto, m.nome AS marca, p.data_validade
FROM produtos p
INNER JOIN marcas m ON p.marca_id = m.id
WHERE p.data_validade < CURRENT_DATE;
