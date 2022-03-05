# softka_tallerSQL

# Primer script

//CRACION BASE DE DATOS TIENDA
create database  if not exists tienda default character set utf8;

//CARGAR BASE DE DATOS
use tienda;

//CREACION DE LA TABLA CLIENTES
create table if not exists customer(
	id_customer int not null auto_increment,
    type_document varchar(25) not null,
    customer_document varchar(10) not null,
    customer_name varchar(30) not null,
    phone varchar(12) null,
    of_delete boolean default(false), 
    primary key (id_customer),
    unique index customer_document_INDEX (customer_document asc)
)engine = InnoDB;

//CREACION DE LA TABLA PRODUCTOS
create table if not exists product(
	id_product int not null auto_increment,
    name_product varchar(45) not null,
    brand varchar(45) not null,
    price double not null,
    type_product varchar(45) null,
    expire_product date null,
    primary key (id_product),
    unique index name_product_INDEX (name_product asc)
)engine = InnoDB;

//CREACION DE LA TABLA PROVEEDORES
create table if not exists provider(
	id_provider int not null auto_increment,
    nit_provider varchar(10) not null,
    name_provider varchar(45) not null,
    primary key (id_provider),
    unique index nit_provider_INDEX (nit_provider asc)
)engine = InnoDB;

//CREACION DE LA TABLA VENTAS
create table if not exists sale(
	id_sale int not null auto_increment,
    customer_document varchar(10) not null,
    date_sale datetime,
    of_delete boolean default(false),
    primary key (id_sale),
    unique index id_sale_INDEX (id_sale asc),
    
    constraint fk_saleCustomer
		foreign key (customer_document)
		references customer (customer_document)
		on delete no action
		on update no action
)engine = InnoDB;

//CREACION DE LA TABLA PRODUCTOS-VENTAS
create table if not exists product_has_sale(
	id int not null auto_increment,
    product_idProduct int not null,
    sale_idSale int not null,
    primary key (id),
    
    constraint fk_productSaleProduct
		foreign key (product_idProduct)
        references product (id_product)
        on delete cascade
		on update cascade,
    constraint fk_productSaleSale
		foreign key (sale_idSale)
        references sale (id_sale)
        on delete cascade
		on update cascade
)engine = InnoDB;

//CREACION DE LA TABLA PRODUCTOS-PROVEEDORES
create table if not exists product_has_provider(
	id int not null auto_increment,
    product_idProduct int not null,
    provider_idProvider int not null,
    primary key (id),
    
    constraint fk_productProviderProduct
		foreign key (product_idProduct)
        references product (id_product)
        on delete no action
		on update no action,
    constraint fk_productProviderProvider
		foreign key (provider_idProvider)
        references provider (id_provider)
        on delete no action
		on update no action
)engine = InnoDB;

//INSERTAR DATOS A LAS TABLAS DE LA BASE DE DATOS TIENDA
insert into customer(id_customer, type_document,customer_document, customer_name, phone) values('1','cc','1112761541', 'Cristian', '3238256496');
insert into customer(id_customer, type_document,customer_document, customer_name, phone) values('2','cc','1112764735', 'Cristian', '3004556164');
insert into provider(id_provider, nit_provider, name_provider) values('1','83600072','Coca-cola');
insert into provider(id_provider, nit_provider, name_provider) values('2','83600073','Postobon');
insert into product(name_product, brand, price, type_product, expire_product) values('sprite', 'toronja', 1500, 'bebida', '2022-10-20');
insert into product(name_product, brand, price, type_product, expire_product) values('hit', 'jugo hit', 2500, 'bebida', '2022-09-15');
insert into sale(customer_document, date_sale) values('1112761541', '2022-03-04');
insert into sale(customer_document, date_sale) values('1112764735', '2022-03-05');
insert into product_has_sale(product_idProduct, sale_idSale) values('1', '1');
insert into product_has_sale(product_idProduct, sale_idSale) values('2', '1');
insert into product_has_sale(product_idProduct, sale_idSale) values('2', '2');

//BORRADO LOGICO DE VENTAS
update sale set of_delete = true where customer_document = '1112764735';
update sale set of_delete = true where customer_document = '1112761541';

//BORRADO FISICO DE VENTAS
delete from product_has_sale where sale_idSale = 1;
delete from sale where customer_document = '1112764735';

# Segundo script

//Consulta SQL donde pueda obtener los productos vendidos digitando tipo de documento número de documento.
select sale.id_sale, sale.customer_document, customer.customer_name, product_idProduct from sale
inner join customer on sale.customer_document = customer.customer_document
inner join product_has_sale on sale.id_sale = product_has_sale.sale_idSale
where sale.customer_document = '1112764735';

//Consultar productos por medio del nombre, el cual debe mostrar quien o quienes han sido sus proveedores.
select *
from tienda.product
inner join product_has_provider on tienda.product.id_product = tienda.product_has_provider.product_idProduct;


drop table product;
drop database if exists tienda;
select * from sale;
delete from product_has_sale where sale_idSale = 1;
