class Empleado:
    def __init__(self, id, nombre, edad, peso, estatura, diabetes, hipertension, corazon, cancer, tabaquismo, vacuna, numDepartamento) -> None:
            self.id             = id
            self.nombre         = nombre
            self.edad           = edad
            self.peso           = peso
            self.estatura       = estatura
            self.diabetes       = diabetes
            self.hipertension   = hipertension
            self.corazon        = corazon
            self.cancer         = cancer
            self.tabaquismo     = tabaquismo
            self.vacuna         = vacuna
            self.numDepartamento = numDepartamento

    def toList(self):
        return [self.id, self.nombre, self.edad, self.peso, self.estatura, self.diabetes, self.hipertension, self.corazon, self.cancer, self.tabaquismo, self.vacuna, self.numDepartamento]

    def toString(self, separator = ","):
        result = self.toList()
        return separator.join([ str(x) for x in result])
        
    def print(self):
        print(self.toString())

    def getRow(self):
        WIDTH = 14
        LINE = "".center(WIDTH,'-')
        sickness = [
            "diabetes".capitalize().center(WIDTH, " ")      if self.diabetes == "1"     else LINE,
            "hipertension".capitalize().center(WIDTH, " ")  if self.hipertension == "1" else LINE,
            "corazon".capitalize().center(WIDTH, " ")       if self.corazon == "1"      else LINE,
            "cancer".capitalize().center(WIDTH, " ")        if self.cancer == "1"       else LINE,
            "tabaquismo".capitalize().center(WIDTH, " ")    if self.tabaquismo == "1"   else LINE

        ]
        sickness = " ".join(sickness)
        vacuna = "con vacuna".capitalize().center(WIDTH, ' ') if self.vacuna == "1" else LINE
        return f"{self.calcular_imc_str()}\t {float2P(self.calcular_imc())}\t {self.id}\t {self.nombre.capitalize().center(20, ' ')}\t {self.peso}\t {self.estatura}\t {sickness}\t\t\t {vacuna}"

    def printRow(self):
        print(self.getRow())

    def calcular_imc(self):
        return self.peso / (self.estatura ** 2)

    def calcular_imc_str(self):
        return imcClassifier(self.calcular_imc())

    def __lt__(self, other):
        return self.calcular_imc() < other.calcular_imc()

def imcClassifier(val):
    if(val < 16):
        return "Delgadez severa\t"
    elif(16 <= val and val < 17):
        return "Delgadez moderada\t"
    elif(17 <= val and val < 18.5):
        return "Delgadez aceptable\t"
    elif(18.5 <= val and val < 25):
        return "Normal\t\t"
    elif(25 <= val and val < 30):
        return "Pre-obeso\t"
    elif(30 <= val and val < 35):
        return "Obeso tipo I\t"
    elif(35 <= val and val < 40):
        return "Obeso tipo II\t"
    else:
        return "Obeso tipo III\t"

def float2P(val):
    return "{:.2f}".format(val)

def empleadoFromString(string : str):
    values = string.split(";")
    return Empleado(
        values[0],
        values[1],
        int(values[2]),
        float(values[3]),
        float(values[4]),
        values[5],
        values[6],
        values[7],
        values[8],
        values[9],
        values[10],
        values[11]
    )

REGISTRO_EMPLEADOS = {}
EMPLEADOS_FILE_NAME = ""
REPORTE_EMPLEADOS_NAME = "reporte_empleados.txt"

def alta_empleado():
    value1 = input("Teclea el id del empleado : ")
    value2 = input("Teclea el nombre del empleado : ")
    value3 = input("Teclea edad del empleado : ")
    value4 = input("Teclea el peso (Kg.) : ")
    value5 = input("Teclea la estatura (Mts.) : ")
    value6 = input("Diabetes 1-Si 0-No ? : ")
    value7 = input("Hipertensión 1-Si 0-No ? : ")
    value8 = input("Enfermedad Corazón 1-Si 0-No ? : ")
    value9 = input("Cáncer 1-Si 0-No ? : ")
    value10 = input("Tabaquismo 1-Si 0-No ? : ")
    value11 = input("Vacuna Covid-19 1-Si 0-No : ")
    value12 = input("Teclea el número de departamento : ")

    newEmpleado = Empleado(
        value1,
        value2,
        int(value3),
        float(value4),
        float(value5),
        value6,
        value7,
        value8,
        value9,
        value10,
        value11,
        value12
    )

    newEmpleado.print()

    REGISTRO_EMPLEADOS[newEmpleado.id] = newEmpleado
    grabar_empleados()

def printHeader():
    temp = "Enfermedades".center(100, " ")
    result = f"Clasificacion IMC\t IMC\t id\t Nombre\t\t\t Peso\t Altura\t {temp}\t Vacuna"
    print(result)
    return result

def calcular_imc():
    empleados = list(REGISTRO_EMPLEADOS.values())
    imcs = [ e.calcular_imc() for e in empleados ]
    union = zip(imcs,empleados)
    sortedE = [ val[1] for val in sorted(union)]

    print()
    lines = [printHeader()]
    for s in sortedE:
        temp = s.getRow()
        lines.append(temp)
        print(temp)
    
    return lines

def actualizar_empleado():
    id = input("Cual es el id? ")
    try:
        empleado : Empleado = REGISTRO_EMPLEADOS[id]
        printHeader()
        empleado.printRow()

        value2 = input("Teclea el nombre del empleado (Presiona enter para cancelar): ")

        if(value2 == ""):
            return None

        newEmpleado = Empleado(
            empleado.id,
            value2,
            int(input("Teclea edad del empleado : ")),
            float(input("Teclea el peso (Kg.) : ")),
            float(input("Teclea la estatura (Mts.) : ")),
            input("Diabetes 1-Si 0-No ? : "),
            input("Hipertensión 1-Si 0-No ? : "),
            input("Enfermedad Corazón 1-Si 0-No ? : "),
            input("Cáncer 1-Si 0-No ? : "),
            input("Tabaquismo 1-Si 0-No ? : "),
            input("Vacuna Covid-19 1-Si 0-No : "),
            input("Teclea el número de departamento : ")
        )

        print()
        printHeader()
        newEmpleado.printRow()

        if(input("actualizar? (si/no) ") == "si"):
            REGISTRO_EMPLEADOS[id] = newEmpleado
            grabar_empleados()

    except KeyError:
        print("El usuario no existe")

def consultar_empleado():
    id = input("Cual es el id? : ")
    try:
        empleado = REGISTRO_EMPLEADOS[id]
        printHeader()
        empleado.printRow()
    except KeyError:
        print("Este usuario no existe")

def crear_reporte_empleados():
    imcLines = calcular_imc()

    employees = REGISTRO_EMPLEADOS.values()

    total = len(employees)

    d = {
        "diabetes" : 0,
        "hipertension" : 0,
        "corazon" : 0,
        "cancer" : 0,
        "tabaquismo" : 0
    }

    for e in employees:
        d["diabetes"] += 1 if e.diabetes == "1" else 0
        d["hipertension"] += 1 if e.hipertension == "1" else 0
        d["corazon"] += 1 if e.corazon == "1" else 0
        d["cancer"] += 1 if e.cancer == "1" else 0
        d["tabaquismo"] += 1 if e.tabaquismo == "1" else 0

    lines = [
        "\nEnfermedad\t Cantidad\t Porcentaje"
    ]
    
    for k in d.keys():
        lines.append(f"{k.capitalize().center(12,' ')}\t\t {d[k]}\t\t {float2P((d[k]/total)*100)}%")
    lines.append(f"Total Empleados {total}")
    
    for line in lines:
        print(line)

    imcLines.extend(lines)

    grabar_archivo(REPORTE_EMPLEADOS_NAME, [ line+'\n' for line in imcLines ])

def salir():
    print("salir")

def leer_archivo(nombre):
    lines = []
    with open(nombre, "r") as f:
        lines = f.readlines()
        f.close()

    lines = [ empleadoFromString(empleado.rstrip()) for empleado in lines ]

    if len(REGISTRO_EMPLEADOS.keys()) == 0:
        for e in lines:
            REGISTRO_EMPLEADOS[e.id] = e

    return lines
            
def grabar_archivo(filename, linesToWrite):
    with open(filename, "w") as f:
        f.writelines(linesToWrite)
        f.close()
    
def grabar_empleados():
    empleados = [ employee.toString(separator=";")+'\n' for employee in REGISTRO_EMPLEADOS.values() ]
    grabar_archivo(EMPLEADOS_FILE_NAME, empleados)

def menu():
    print("""
        1.- Alta de empleado
        2.- Calcular IMC de Empleados
        3.- Cambia infromacion del empleado
        4.- Consulta un empleado
        5.- Reporte de todos los empleados
        6.- Salir""")

    return int(input("Teclea la opcion: "))

def main():
    global EMPLEADOS_FILE_NAME

    try:

        if EMPLEADOS_FILE_NAME == "":
            # EMPLEADOS_FILE_NAME = "base_de_datos_empleados.csv"
            EMPLEADOS_FILE_NAME = input("Cual es el nombre del archivo a leer? ")
            leer_archivo(EMPLEADOS_FILE_NAME)

    except FileNotFoundError:
        print("No se encontro ningun archivo con ese nombre".upper().center(50, "="))
        return None


    option = menu()

    if (option < 1 or option > 6):
        print("Opcion incorrecta")
    elif(option == 1):
        alta_empleado()
    elif(option == 2):
        calcular_imc()
    elif(option == 3):
        actualizar_empleado()
    elif(option == 4):
        consultar_empleado()
    elif(option == 5):
        crear_reporte_empleados()
    elif(option == 6):
        salir()
        return None

    print("")
    main()

def test():
    REGISTRO_EMPLEADOS["1"] = Empleado("1", "Javier",   24,     88.1, 1.78, "1", "0", "1", "0", "0", "1", "420")
    REGISTRO_EMPLEADOS["2"] = Empleado("2", "Diana",    22,     48, 1.5,    "0", "1", "0", "1", "0", "1", "69")
    REGISTRO_EMPLEADOS["3"] = Empleado("3", "Estefania", 24,    50, 1.61,   "0", "1", "1", "1", "1", "0", "15")
    REGISTRO_EMPLEADOS["4"] = Empleado("4", "Fernando",  60,    50.1, 1.88, "1", "0", "1", "0", "0", "1", "16")
    REGISTRO_EMPLEADOS["5"] = Empleado("5", "Carlos",    19,    67.9, 1.69,    "0", "1", "1", "1", "0", "1", "17")
    REGISTRO_EMPLEADOS["6"] = Empleado("6", "Lalo",     24,     70, 1.8,    "0", "1", "0", "1", "1", "1", "A")
    REGISTRO_EMPLEADOS["7"] = Empleado("7", "Alexis",   23,     102, 1.78,  "1", "0", "1", "0", "0", "1", "120")
    REGISTRO_EMPLEADOS["8"] = Empleado("8", "Mario",    21,     40, 1.9,    "0", "1", "1", "1", "1", "1", "Z")
    REGISTRO_EMPLEADOS["9"] = Empleado("9", "Mario",    21,     40, 1.9,    "0", "1", "1", "1", "1", "1", "Z")
    REGISTRO_EMPLEADOS["10"] = Empleado("10", "Javier",   24,     88.1, 1.78, "1", "0", "1", "0", "0", "1", "420")
    REGISTRO_EMPLEADOS["20"] = Empleado("20", "Diana",    22,     48, 1.5,    "0", "1", "0", "1", "0", "1", "69")
    REGISTRO_EMPLEADOS["30"] = Empleado("30", "Estefania", 24,    50, 1.61,   "0", "1", "1", "1", "1", "0", "15")
    REGISTRO_EMPLEADOS["40"] = Empleado("40", "Fernando",  60,    50.1, 1.88, "1", "0", "1", "0", "0", "1", "16")
    REGISTRO_EMPLEADOS["50"] = Empleado("50", "Carlos",    19,    67.9, 1.69,    "0", "1", "1", "1", "0", "1", "17")
    REGISTRO_EMPLEADOS["60"] = Empleado("60", "Lalo",     24,     70, 1.8,    "0", "1", "0", "1", "1", "1", "A")
    REGISTRO_EMPLEADOS["70"] = Empleado("70", "Alexis",   23,     102, 1.78,  "1", "0", "1", "0", "0", "1", "120")
    REGISTRO_EMPLEADOS["80"] = Empleado("80", "Mario",    21,     40, 1.9,    "0", "1", "1", "1", "1", "1", "Z")
    REGISTRO_EMPLEADOS["90"] = Empleado("90", "Mario",    21,     40, 1.9,    "0", "1", "1", "1", "1", "1", "Z")
    
    grabar_empleados()

def test2():
    testArr = leer_archivo(EMPLEADOS_FILE_NAME)

    for e in testArr:
        e.print()

def test3():
    leer_archivo(EMPLEADOS_FILE_NAME)
    crear_reporte_empleados()

if __name__ == "__main__":
    main()
    # test()
    # test2()
    # test3()
