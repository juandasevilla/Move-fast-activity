# Move-fast-activity

#scripts para crear la bases de datos
create table sucursales(
id SERIAL PRIMARY KEY,
nombre VARCHAR(250) NOT NULL,
ciudad VARCHAR(250) NOT NULL
);



create table vehiculos (
placa VARCHAR(20) PRIMARY KEY,
marca VARCHAR(100),
modelo VARCHAR(100),
año INT CHECK (año BETWEEN 2000 AND 2025) NOT NULL,
disponible BOOLEAN DEFAULT TRUE,
sucursal_id INT,

FOREIGN KEY (sucursal_id) REFERENCES sucursales(id) ON UPDATE CASCADE
);

create table clientes(
identificacion VARCHAR(250) PRIMARY KEY,
nombre VARCHAR(250) NOT NULL,
apellido VARCHAR(250) NOT NULL
);

create table alquileres(
id SERIAL PRIMARY KEY,
cliente_id VARCHAR(250),
placa VARCHAR(250) NOT NULL,
fecha_inicio DATE NOT NULL,
fecha_final DATE NOT NULL,
activo BOOLEAN NOT NULL,
FOREIGN KEY (cliente_id) REFERENCES clientes(identificacion)ON DELETE CASCADE,
FOREIGN KEY (placa) REFERENCES vehiculos(placa)
);

create table pagos(
id SERIAL PRIMARY KEY,
valor FLOAT NOT NULL,
fecha DATE NOT NULL,
alquiler_id INT ,
es_credito BOOLEAN NOT NULL,

FOREIGN KEY (alquiler_id) REFERENCES alquileres(id) ON DELETE SET NULL
);

#script para insertar datos

-- Insertar en sucursales
INSERT INTO sucursales (nombre, ciudad) VALUES 
('Sucursal Norte', 'Bogotá'),
('Sucursal Sur', 'Medellín'),
('Sucursal Centro', 'Cali'),
('Sucursal Este', 'Barranquilla'),
('Sucursal Oeste', 'Cartagena');

-- Insertar en vehiculos
INSERT INTO vehiculos (placa, marca, modelo, año, disponible, sucursal_id) VALUES
('ABC123', 'Toyota', 'Corolla', 2020, TRUE, 1),
('XYZ789', 'Chevrolet', 'Spark', 2018, TRUE, 2),
('LMN456', 'Renault', 'Duster', 2021, TRUE, 3),
('JKL321', 'Mazda', 'CX-5', 2019, TRUE, 4),
('HGT987', 'Nissan', 'Versa', 2022, TRUE, 5),
('QWE123', 'Ford', 'Escape', 2017, TRUE, 1),
('ASD456', 'Hyundai', 'Tucson', 2023, TRUE, 2),
('ZXC789', 'Kia', 'Sportage', 2021, TRUE, 3);

-- Insertar en clientes
INSERT INTO clientes (identificacion, nombre, apellido) VALUES
('1001', 'Carlos', 'Lopez'),
('1002', 'Maria', 'Gomez'),
('1003', 'Juan', 'Perez'),
('1004', 'Ana', 'Martinez'),
('1005', 'Luis', 'Torres');

-- Insertar en alquileres
INSERT INTO alquileres (cliente_id, placa, fecha_inicio,fecha_final, activo) VALUES
('1001', 'ABC123', '2025-04-01','2025-04-05', FALSE),
('1002', 'XYZ789', '2025-04-02','2025-05-01', TRUE),
('1003', 'LMN456', '2025-04-03','2025-04-10', FALSE),
('1004', 'JKL321', '2025-04-04','2025-06-01', TRUE),
('1005', 'HGT987', '2025-04-05','2025-05-01', TRUE);

-- Insertar en pagos
INSERT INTO pagos (valor, fecha, alquiler_id, es_credito) VALUES
(500.0, '2025-04-01', 1, TRUE),
(400.0, '2025-04-02', 2, FALSE),
(600.0, '2025-04-03', 3, TRUE),
(450.0, '2025-04-04', 4, FALSE),
(700.0, '2025-04-05', 5, TRUE);

#Script para consultas

4.
SELECT *
FROM PUBLIC.vehiculos
INNER JOIN sucursales
ON vehiculos.sucursal_id = sucursales.id
AND sucursales.ciudad = 'Cali';

5.
SELECT *
FROM public.alquileres
INNER JOIN clientes On alquileres.cliente_id = clientes.identificacion
Inner JOIN vehiculos ON alquileres.placa = vehiculos.placa
WHERE alquileres.activo = TRUE;

6.
SELECT v.sucursal_id, s.nombre AS sucursal_nombre, SUM(p.valor) AS ingresos_totales
FROM vehiculos v
JOIN alquileres a ON v.placa = a.placa
JOIN pagos p ON a.id = p.alquiler_id
JOIN sucursales s ON v.sucursal_id = s.id
WHERE v.placa IN (
    SELECT placa FROM alquileres GROUP BY placa HAVING COUNT(id) > 3
)
GROUP BY v.sucursal_id, s.nombre
ORDER BY ingresos_totales DESC;

7
SELECT *  
FROM vehiculos  
WHERE placa IN (  
    SELECT placa  
    FROM alquileres  
    GROUP BY placa  
    HAVING COUNT(id) > 5 
);

8.
SELECT SUM(pagos.valor) AS ingresos_totales
FROM pagos
