# CURSOR
CREATE DATABASE supermercado;
USE supermercado;

CREATE TABLE productos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    precio DECIMAL(10,2),
    cantidad INT
);

INSERT INTO productos (nombre, precio, cantidad) VALUES
('Manzanas', 1.50, 10),
('Leche', 0.90, 5),
('Pan', 1.20, 15),
('Huevos', 2.50, 12);

DELIMITER $$

CREATE DEFINER=`root`@`localhost` PROCEDURE `lista_productos_carrito`(INOUT productoslist VARCHAR(4000))
BEGIN
  
    DECLARE finished INTEGER DEFAULT 0;
    DECLARE productname VARCHAR(100) DEFAULT '';
    DECLARE curproducto CURSOR FOR
        SELECT nombre FROM productos;

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET finished = 1;
    OPEN curproducto;
    
    obtener_productos: LOOP
		FETCH curproducto INTO productname;
       
        IF finished = 1 THEN
            LEAVE obtener_productos;
        END IF;
        
        SET productoslist = CONCAT(productname, ';', productoslist);
    END LOOP obtener_productos;

  
    CLOSE curproducto;
END $$

DELIMITER ;

SET @lista_productos = '';
CALL lista_productos_carrito(@lista_productos);
SELECT @lista_productos;
