# examen-p2


  import sqlite3

class DataAccess:
    def __init__(self, db_file):
        self.conn = sqlite3.connect(db_file)
        self.cursor = self.conn.cursor()
        self.create_table()

    def create_table(self):
        self.cursor.execute('''CREATE TABLE IF NOT EXISTS trabajadores (
                                id INTEGER PRIMARY KEY,
                                nombre TEXT,
                                apellidos TEXT,
                                sueldo_bruto REAL,
                                categoria TEXT)''')
        self.conn.commit()

    def insert_trabajador(self, nombre, apellidos, sueldo_bruto, categoria):
        self.cursor.execute('''INSERT INTO trabajadores (nombre, apellidos, sueldo_bruto, categoria)
                                VALUES (?, ?, ?, ?)''', (nombre, apellidos, sueldo_bruto, categoria))
        self.conn.commit()

    def fetch_all_trabajadores(self):
        self.cursor.execute('''SELECT * FROM trabajadores''')
        return self.cursor.fetchall()

# Capa de lógica de negocio (Business Logic Layer)
class BusinessLogic:
    def calcular_aumento_sueldo(self, sueldo_bruto):
        if sueldo_bruto <= 1000:
            return sueldo_bruto * 0.1
        elif sueldo_bruto <= 2000:
            return sueldo_bruto * 0.2
        elif sueldo_bruto <= 4000:
            return sueldo_bruto * 0.3
        else:
            return sueldo_bruto * 0.4

    def calcular_sueldo_neto(self, sueldo_bruto):
        aumento = self.calcular_aumento_sueldo(sueldo_bruto)
        return sueldo_bruto + aumento

# Capa de cliente
if __name__ == "__main__":
    db_file = "empresa.db"
    data_access = DataAccess(db_file)
    business_logic = BusinessLogic()

    # Ingreso de trabajadores de ejemplo
    trabajadores = [
        ("Ana", "López", 900, "A"),
        ("Juan", "García", 1500, "B"),
        ("María", "Martínez", 3000, "C"),
        ("Carlos", "Pérez", 5000, "A")
    ]

    # Insertar trabajadores en la base de datos
    for nombre, apellidos, sueldo_bruto, categoria in trabajadores:
        data_access.insert_trabajador(nombre, apellidos, sueldo_bruto, categoria)

    # Mostrar lista de empleados ingresados
    print("Lista de empleados:")
    total_sueldos_netos = 0
    for trabajador in data_access.fetch_all_trabajadores():
        nombre, apellidos, sueldo_bruto, categoria = trabajador
        aumento = business_logic.calcular_aumento_sueldo(sueldo_bruto)
        sueldo_neto = business_logic.calcular_sueldo_neto(sueldo_bruto)
        total_sueldos_netos += sueldo_neto
        print(f"{nombre} {apellidos} - Sueldo Bruto: {sueldo_bruto} - Monto Aumento: {aumento} - Sueldo Neto: {sueldo_neto}")

    print(f"\nMonto total de Sueldos Netos: {total_sueldos_netos}")
