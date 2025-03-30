# ProjetoBDEcommerce
Projeto de SQL da DIO

-- Estrutura Inicial do Banco de Dados
-- Criado um banco de dados chamado ecommerce.
-- Incluídas tabelas principais para representar os dados:
--   - clients: informações dos clientes.
--   - product: detalhes dos produtos.
--   - orders: pedidos realizados pelos clientes.
--   - productStorage: controle de estoque.
--   - supplier: fornecedores cadastrados.
--   - seller: vendedores cadastrados.
--   - Tabelas de relacionamento (productSeller, productOrder, StorageLocation, productSupplier).

-- Alterações e Refinamentos
-- 1. Identificação de Clientes PJ e PF:
--    - Adicionado o atributo clientType para definir o tipo de cliente (PJ ou PF).
--    - Criados campos separados para CNPJ e CPF, com regras para garantir que um cliente não tenha ambos.
--    - Incluído um check constraint para validar as condições.

-- 2. Formas de Pagamento:
--    - Criada a tabela paymentMethods, permitindo múltiplos métodos de pagamento por pedido.
--    - Estrutura que inclui tipo de pagamento (ex.: Pix, Boleto) e valor.

-- 3. Gestão de Entregas:
--    - Adicionada a tabela delivery para armazenar informações relacionadas a entregas.
--    - Campos para código de rastreio e status da entrega (ex.: Enviado, Em trânsito).

-- Funcionalidades Adicionais
-- - Consultas complexas utilizando JOINs:
--   - Contagem de pedidos por cliente.
--   - Relação de vendedores que também são fornecedores.
--   - Vínculo entre produtos, fornecedores e estoques.
-- - Filtros e agrupamentos:
--   - Uso do WHERE para filtros detalhados.
--   - Aplicação de HAVING para condições em grupos de dados.
-- - Atributos derivados e ordenações:
--   - Criação de atributos calculados, como contagem total de pedidos.
--   - Utilização de ORDER BY para ordenar os resultados.


-- criação do banco de dados para o cenário de E-comerce
create database ecommerce;
use ecommerce;

-- Criar tabela cliente
Create table clients(
		idClient int auto_increment primary key,
        Fname varchar(10),
        Minit char(3),
        Lname varchar(20),
        CPF char(11) not null,
        Addres varchar(30),
        constraint unique_cpf_client unique (CPF)
);


-- Criar tabela produto
Create table product(
		idProduct int auto_increment primary key,
        Pname varchar(10),
        classification_kids bool,
        category enum('Eletronico', 'Investimento', 'Brinquedos', 'Alimentos', 'Moveis') not null,
        avaliação float default 0,
        size varchar(10)
);

alter table clients auto_increment=1;

-- Criar tabela pedido
create table orders(
		idOrder int auto_increment primary key,
        idOrderClient int,
        orderStatus enum('Cancelado', 'Confirmado', 'Em processamento') default 'Em processamento',
        orderDescription varchar(255),
        sendValue float default 10,
        paymentCash bool default false,
        constraint fk_ordes_client foreign key (idOrderClient) references clients(idClient)
);

-- Criar tabela estoque
create table productStorage(
		idProdStorage int auto_increment primary key,
        storageLocation varchar(255),
        quantity int default 0
);
-- Criar tabela fornecedor
create table supplier(
		idSupplier int auto_increment primary key,
        SocialName varchar(255) not null unique,
        CNPJ char(15) not null unique,
        contact char(11) not null
);
-- Criar tabela vendedor
create table seller(
		idSeller int auto_increment primary key,
        SocialName varchar(255) not null,
        AbstName varchar(255),
        CNPJ char(15),
        CPF char(9),
        location varchar(255),
        contact char(11) not null,
        constraint unique_cnpj_seller unique (CNPJ),
        constraint unique_cpf_seller unique (CNPJ)
);


create table productSeller(
idPseller int,
idProduct int,
Quantity int default 1,
primary key (idPseller, idProduct),
constraint fk_product_seller foreign key (idPseller) references seller(idSeller),
constraint fk_product_product foreign key (idProduct) references product(idProduct)
);

create table productOrder(
idPproduct int,
idPorder int,
poQuantity int default 1,
poStatus enum('Disponível', 'Fora de estoque') default 'Disponível',
primary key (idPproduct, idPorder),
constraint fk_productorder_seller foreign key (idPproduct) references product(idProduct),
constraint fk_productorder_product foreign key (idPorder) references orders(idOrder)
);

create table StorageLocation(
idLproduct int,
idLstorage int,
location varchar(255) not null,
primary key (idLproduct, idLstorage),
constraint fk_storage_location_product foreign key (idLproduct) references product(idProduct),
constraint fk_storage_location_storage foreign key (idLstorage) references productStorage(idProdStorage)
);

create table productSupplier(
		idPsSupplier int,
        idPsProduct int,
        quantity int not null,
        primary key (idPsSupplier, idPsProduct),
        constraint fk_product_supplier_supplier foreign key (idPsSupplier) references supplier(idSupplier),
        constraint fk_product_supplier_product foreign key (idPsProduct) references product(idProduct)
);

INSERT INTO clients (Fname, Minit, Lname, CPF, Addres) VALUES
('Carlos', 'A.', 'Silva', '12345678901', 'Rua A, 123'),
('Maria', 'B.', 'Souza', '23456789012', 'Av. B, 456'),
('João', 'C.', 'Oliveira', '34567890123', 'Travessa C, 789'),
('Ana', 'D.', 'Almeida', '45678901234', 'Rua D, 321'),
('Pedro', 'E.', 'Santos', '56789012345', 'Av. E, 654'),
('Beatriz', 'F.', 'Moraes', '67890123456', 'Travessa F, 987'),
('Lucas', 'G.', 'Costa', '78901234567', 'Rua G, 246'),
('Fernanda', 'H.', 'Martins', '89012345678', 'Av. H, 135'),
('Daniel', 'I.', 'Nunes', '90123456789', 'Travessa I, 864'),
('Juliana', 'J.', 'Rodrigues', '01234567890', 'Rua J, 579');


INSERT INTO product (Pname, classification_kids, category, avaliação, size) VALUES
('Laptop', false, 'Eletronico', 4.5, 'Médio'),
('InvestBook', false, 'Investimento', 5.0, 'Pequeno'),
('ToyCar', true, 'Brinquedos', 4.2, 'Pequeno'),
('Smartphone', false, 'Eletronico', 4.8, 'Pequeno'),
('Doll', true, 'Brinquedos', 4.0, 'Pequeno'),
('Chair', false, 'Moveis', 4.3, 'Grande'),
('BookShelf', false, 'Moveis', 4.7, 'Grande'),
('Puzzle', true, 'Brinquedos', 4.1, 'Médio'),
('TV', false, 'Eletronico', 4.9, 'Grande'),
('RiceBag', false, 'Alimentos', 4.5, 'Grande');


INSERT INTO orders (idOrderClient, orderStatus, orderDescription, sendValue, paymentCash) VALUES
(1, 'Confirmado', 'Compra de produtos eletrônicos', 25.0, true),
(2, 'Em processamento', 'Pedido de brinquedos para crianças', 18.5, false),
(3, 'Cancelado', 'Pedido de móveis para escritório', 30.0, true),
(4, 'Confirmado', 'Compra de alimentos não perecíveis', 15.0, false),
(5, 'Em processamento', 'Pedido de itens de investimento', 50.0, true),
(6, 'Confirmado', 'Pedido de brinquedos educativos', 22.0, false),
(7, 'Cancelado', 'Compra de dispositivos eletrônicos', 40.0, true),
(8, 'Confirmado', 'Pedido de cadeiras e mesas', 35.0, true),
(9, 'Em processamento', 'Compra de suprimentos alimentares', 27.0, false),
(10, 'Confirmado', 'Pedido de utensílios eletrônicos', 45.0, true);

INSERT INTO productStorage (storageLocation, quantity) VALUES
('Armazém Central', 100),
('Depósito Norte', 150),
('Armazém Sul', 200),
('Depósito Leste', 250),
('Armazém Oeste', 300),
('Depósito Secundário', 175),
('Armazém Principal', 120),
('Depósito Temporário', 80),
('Armazém Especial', 90),
('Depósito Frio', 60);

INSERT INTO supplier (SocialName, CNPJ, contact) VALUES
('Fornecedor Eletrônico', '12345678901234', '98765432101'),
('Alimentos do Sul', '23456789012345', '87654321012'),
('Moveis Únicos', '34567890123456', '76543210987'),
('Brinquedos Estrela', '45678901234567', '65432109876'),
('Investimentos BR', '56789012345678', '54321098765'),
('TecnoSul', '67890123456789', '43210987654'),
('Alimentos Frescos', '78901234567890', '32109876543'),
('Madeira Forte', '89012345678901', '21098765432'),
('Estrela Kids', '90123456789012', '10987654321'),
('Móveis Elegantes', '01234567890123', '09876543210');

INSERT INTO seller (SocialName, AbstName, CNPJ, CPF, location, contact) VALUES
('Vendedor Sul', 'SulTech', '12345678901234', '123456789', 'Porto Alegre, RS', '98765432101'),
('Mega Comércio', 'MegaCom', '23456789012345', '234567890', 'Florianópolis, SC', '87654321012'),
('Loja Central', 'CenStore', '34567890123456', '345678901', 'Curitiba, PR', '76543210987'),
('Mercado Real', 'MercReal', '45678901234567', '456789012', 'São Paulo, SP', '65432109876'),
('Comércio Livre', 'FreeCom', '56789012345678', '567890123', 'Rio de Janeiro, RJ', '54321098765'),
('Super Vendas', 'SuperSell', '67890123456789', '678901234', 'Belo Horizonte, MG', '43210987654'),
('Comércio Eletrônico', 'E-Com', '78901234567890', '789012345', 'Salvador, BA', '32109876543'),
('Mundo Kids', 'KidsWorld', '89012345678901', '890123456', 'Fortaleza, CE', '21098765432'),
('TechStore', 'TStore', '90123456789012', '901234567', 'Recife, PE', '10987654321'),
('Varejo Express', 'Express', '01234567890123', '012345678', 'Manaus, AM', '09876543210');

INSERT INTO productSeller (idPseller, idProduct, Quantity) VALUES
(1, 1, 5),
(2, 2, 10),
(3, 3, 15),
(4, 4, 20),
(5, 5, 25),
(6, 6, 30),
(7, 7, 35),
(8, 8, 40),
(9, 9, 45),
(10, 10, 50);

INSERT INTO productOrder (idPproduct, idPorder, poQuantity, poStatus) VALUES
(1, 1, 2, 'Disponível'),
(2, 2, 1, 'Fora de estoque'),
(3, 3, 3, 'Disponível'),
(4, 4, 5, 'Disponível'),
(5, 5, 4, 'Fora de estoque'),
(6, 6, 2, 'Disponível'),
(7, 7, 6, 'Fora de estoque'),
(8, 8, 3, 'Disponível'),
(9, 9, 8, 'Disponível'),
(10, 10, 5, 'Fora de estoque');

INSERT INTO StorageLocation (idLproduct, idLstorage, location) VALUES
(1, 1, 'Depósito Central - Setor A'),
(2, 1, 'Depósito Central - Setor B'),
(3, 2, 'Depósito Norte - Setor C'),
(4, 2, 'Depósito Norte - Setor D'),
(5, 3, 'Depósito Sul - Setor E'),
(6, 3, 'Depósito Sul - Setor F'),
(7, 4, 'Depósito Leste - Setor G'),
(8, 4, 'Depósito Leste - Setor H'),
(9, 5, 'Depósito Oeste - Setor I'),
(10, 5, 'Depósito Oeste - Setor J');

INSERT INTO productSupplier (idPsSupplier, idPsProduct, quantity) VALUES
(1, 1, 100),
(2, 2, 150),
(3, 3, 200),
(4, 4, 250),
(5, 5, 300),
(6, 6, 180),
(7, 7, 220),
(8, 8, 140),
(9, 9, 190),
(10, 10, 210);

-- Atualizar tabela clients
ALTER TABLE clients
ADD clientType ENUM('PJ', 'PF') NOT NULL,
ADD CNPJ CHAR(14),
MODIFY CPF CHAR(11) NULL;

UPDATE clients
SET clientType = CASE
    WHEN CNPJ IS NOT NULL AND CPF IS NULL THEN 'PJ'
    WHEN CPF IS NOT NULL AND CNPJ IS NULL THEN 'PF'
END
WHERE (CNPJ IS NOT NULL AND CPF IS NULL) OR (CPF IS NOT NULL AND CNPJ IS NULL);

-- Garantir que não seja possível preencher CPF e CNPJ ao mesmo tempo
ALTER TABLE clients
ADD CONSTRAINT check_client_type CHECK (
    (clientType = 'PJ' AND CNPJ IS NOT NULL AND CPF IS NULL) OR
    (clientType = 'PF' AND CPF IS NOT NULL AND CNPJ IS NULL)
);

-- Criar tabela para métodos de pagamento
CREATE TABLE paymentMethods (
    idPayment INT AUTO_INCREMENT PRIMARY KEY,
    idOrder INT NOT NULL,
    paymentType ENUM('Cartão', 'Boleto', 'Pix', 'Dinheiro') NOT NULL,
    paymentValue FLOAT NOT NULL,
    FOREIGN KEY (idOrder) REFERENCES orders(idOrder)
);

-- Criar tabela para informações de entrega
CREATE TABLE delivery (
    idDelivery INT AUTO_INCREMENT PRIMARY KEY,
    idOrder INT NOT NULL,
    trackingCode VARCHAR(50),
    deliveryStatus ENUM('Enviado', 'Em trânsito', 'Entregue', 'Cancelado') DEFAULT 'Enviado',
    FOREIGN KEY (idOrder) REFERENCES orders(idOrder)
);

-- Pedidos feitos por cada cliente
SELECT 
    c.Fname AS NomeCliente, 
    c.Lname AS SobrenomeCliente, 
    COUNT(o.idOrder) AS TotalPedidos
FROM clients c
LEFT JOIN orders o ON c.idClient = o.idOrderClient
GROUP BY c.idClient
ORDER BY TotalPedidos DESC;

-- Algum vendedor também é fornecedor
SELECT 
    s.SocialName AS NomeVendedorFornecedor
FROM seller s
INNER JOIN supplier su 
    ON s.SocialName = su.SocialName OR s.contact = su.contact;
    
    -- Relação de produtos, fornecedores e estoques
SELECT 
    p.Pname AS NomeProduto,
    su.SocialName AS NomeFornecedor,
    ps.storageLocation AS LocalEstoque,
    ps.quantity AS QuantidadeEstoque
FROM product p
INNER JOIN productSupplier psu ON p.idProduct = psu.idPsProduct
INNER JOIN supplier su ON su.idSupplier = psu.idPsSupplier
INNER JOIN productStorage ps ON ps.idProdStorage = p.idProduct
ORDER BY ps.quantity DESC;

-- Relação de nomes de fornecedores e nomes de produtos
SELECT 
    su.SocialName AS NomeFornecedor, 
    p.Pname AS NomeProduto
FROM product p
INNER JOIN productSupplier psu ON p.idProduct = psu.idPsProduct
INNER JOIN supplier su ON su.idSupplier = psu.idPsSupplier
ORDER BY su.SocialName, p.Pname;

