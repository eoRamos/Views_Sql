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

CREATE VIEW produtos_com_marcas AS
SELECT prd.*, mrc.mrc_nome AS marca_nome
FROM produtos prd
JOIN marcas mrc ON prd.prd_marcaid = mrc.mrc_id;

CREATE VIEW produtos_com_fornecedores AS
SELECT prd.*, frn.frn_nome AS fornecedor_nome
FROM produtos prd
JOIN produto_fornecedor pf ON prd.prd_id = pf.pf_prod_id
JOIN fornecedores frn ON pf.pf_forn_id = frn.frn_id;

CREATE VIEW produtos_com_fornecedores_marcas AS
SELECT prd.*, mrc.mrc_nome AS marca_nome, frn.frn_nome AS fornecedor_nome
FROM produtos prd
JOIN marcas mrc ON prd.prd_marcaid = mrc.mrc_id
JOIN produto_fornecedor pf ON prd.prd_id = pf.pf_prod_id
JOIN fornecedores frn ON pf.pf_forn_id = frn.frn_id;

CREATE VIEW produtos_estoque_minimo AS
SELECT *
FROM produtos
WHERE prd_qtd_estoque < prd_estoque_min;

ALTER TABLE produtos
ADD COLUMN prd_data_validade DATE;

-- Exemplo de inserção de novos produtos com data de validade
INSERT INTO produtos (prd_nome, prd_qtd_estoque, prd_estoque_min, prd_dat_fabricacao, prd_perecivel, prd_valor, prd_marcaid, prd_data_validade)
VALUES ('Produto 1', 10, 5, NOW(), true, 20.00, 1, '2024-04-30'),
       ('Produto 2', 20, 10, NOW(), false, 15.50, 2, '2024-06-15');

CREATE VIEW produtos_vencidos AS
SELECT prd.*, mrc.mrc_nome AS marca_nome
FROM produtos prd
JOIN marcas mrc ON prd.prd_marcaid = mrc.mrc_id
WHERE prd.prd_data_validade < CURDATE();

