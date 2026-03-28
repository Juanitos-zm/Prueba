Se muestra en este readme, como estan creadas nuestras tablas para la base de datos:
#*Tabla de Familias Linguisticas

CREATE TABLE IF NOT EXISTS FAMILIA_LINGUISTICA (
    id_familia INT NOT NULL AUTO_INCREMENT,
    nombre_familia VARCHAR(100) NOT NULL,
    origen_historico TEXT,
    fecha_registro TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id_familia),
    UNIQUE KEY uk_nombre_familia (nombre_familia)
) ;

#*Tabla  de Niveles de riesgo
CREATE TABLE IF NOT EXISTS NIVEL_RIESGO (
    id_riesgo INT NOT NULL AUTO_INCREMENT,
    nivel VARCHAR(50) NOT NULL,
    descripcion TEXT,
    PRIMARY KEY (id_riesgo),
    UNIQUE KEY uk_nivel (nivel)
);

#*Tabla de Estados de la República
CREATE TABLE IF NOT EXISTS ESTADO (
    id_estado INT NOT NULL,
    nombre_estado VARCHAR(100) NOT NULL,
    abreviatura VARCHAR(10),
    PRIMARY KEY (id_estado),
    UNIQUE KEY uk_nombre_estado (nombre_estado)
);

#*Tabla de Municipios
CREATE TABLE IF NOT EXISTS MUNICIPIO (
    id_municipio INT NOT NULL,
    fk_id_estado INT NOT NULL,
    nombre_municipio VARCHAR(100) NOT NULL,
    PRIMARY KEY (id_municipio),
    FOREIGN KEY (fk_id_estado) REFERENCES ESTADO(id_estado) ON DELETE RESTRICT ON UPDATE CASCADE,
    UNIQUE KEY uk_municipio_estado (fk_id_estado, nombre_municipio)
);

#*Tabla de Localidades
CREATE TABLE IF NOT EXISTS LOCALIDAD (
    id_localidad INT NOT NULL,
    fk_id_municipio INT NOT NULL,
    nombre_localidad VARCHAR(200) NOT NULL,
    codigo_postal VARCHAR(10),
    PRIMARY KEY (id_localidad),
    FOREIGN KEY (fk_id_municipio) REFERENCES MUNICIPIO(id_municipio) ON DELETE RESTRICT ON UPDATE CASCADE,
    UNIQUE KEY uk_localidad_municipio (fk_id_municipio, nombre_localidad)
);

#*Tabla de Agrupaciones Linguisticas
CREATE TABLE IF NOT EXISTS AGRUPACION_LINGUISTICA (
    id_agrupacion INT NOT NULL AUTO_INCREMENT,
    fk_id_familia INT NOT NULL,
    nombre_agrupacion VARCHAR(100) NOT NULL,
    pueblo_indigena_asociado VARCHAR(100),
    notas_historicas TEXT,
    PRIMARY KEY (id_agrupacion),
    FOREIGN KEY (fk_id_familia) REFERENCES FAMILIA_LINGUISTICA(id_familia) ON DELETE RESTRICT ON UPDATE CASCADE,
    UNIQUE KEY uk_agrupacion_familia (fk_id_familia, nombre_agrupacion)
);

#*Tabla de Variantes linguisticas
CREATE TABLE IF NOT EXISTS VARIANTE_LINGUISTICA (
    id_variante INT NOT NULL AUTO_INCREMENT,
    fk_id_agrupacion INT NOT NULL,
    fk_id_riesgo INT,
    nombre_variante VARCHAR(255) NOT NULL,
    clave_iso VARCHAR(10),
    estatus VARCHAR(20) NOT NULL DEFAULT 'Activo',
    fecha_registro TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    fecha_actualizacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id_variante),
    FOREIGN KEY (fk_id_agrupacion) REFERENCES AGRUPACION_LINGUISTICA(id_agrupacion) ON DELETE RESTRICT ON UPDATE CASCADE,
    FOREIGN KEY (fk_id_riesgo) REFERENCES NIVEL_RIESGO(id_riesgo) ON DELETE SET NULL ON UPDATE CASCADE,
    UNIQUE KEY uk_nombre_variante_agrupacion (fk_id_agrupacion, nombre_variante)
);
#*Tabla de Autodenominaciones
CREATE TABLE IF NOT EXISTS AUTODENOMINACION (
    id_autodenominacion INT NOT NULL AUTO_INCREMENT,
    fk_id_variante INT NOT NULL,
    texto_autodenominacion VARCHAR(255) NOT NULL,
    tipo VARCHAR(50) NOT NULL DEFAULT 'Principal',
    PRIMARY KEY (id_autodenominacion),
    FOREIGN KEY (fk_id_variante) REFERENCES VARIANTE_LINGUISTICA(id_variante) ON DELETE CASCADE ON UPDATE CASCADE,
    UNIQUE KEY uk_autodenominacion_variante (fk_id_variante, texto_autodenominacion)
) ;
#*Tabla de Relacion variante-localidad
CREATE TABLE IF NOT EXISTS VARIANTE_LOCALIDAD (
    id_variante_localidad INT NOT NULL AUTO_INCREMENT,
    fk_id_variante INT NOT NULL,
    fk_id_localidad INT NOT NULL,
    porcentaje_hablantes DECIMAL(5,2),
    fecha_fuente_dato DATE,
    PRIMARY KEY (id_variante_localidad),
    FOREIGN KEY (fk_id_variante) REFERENCES VARIANTE_LINGUISTICA(id_variante) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (fk_id_localidad) REFERENCES LOCALIDAD(id_localidad) ON DELETE CASCADE ON UPDATE CASCADE,
    UNIQUE KEY uk_variante_localidad (fk_id_variante, fk_id_localidad)
);

