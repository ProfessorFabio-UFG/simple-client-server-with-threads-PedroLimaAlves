Pedro Henrique Lima – 202206799

a) usando threads apenas no servidor

Código servidor_threads.py:

import socket

import threading

import time


def handle_client(conn, addr):


    print(f"Conexão de {addr}")
    data = conn.recv(1024).decode()
    if not data:
        return
    try:
        op, num1, num2 = data.split()
        num1, num2 = float(num1), float(num2)

        # Simula tempo de processamento
        time.sleep(2)

        if op == "add":
            result = num1 + num2
        elif op == "subtract":
            result = num1 - num2
        elif op == "multiply":
            result = num1 * num2
        elif op == "divide":
            result = num1 / num2 if num2 != 0 else "Erro: Divisão por zero"
        else:
            result = "Erro: Operação inválida"

        conn.send(str(result).encode())

    except Exception as e:
        conn.send(f"Erro: {e}".encode())
    finally:
        conn.close()

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("0.0.0.0", 5678))

server.listen(5)

print("Servidor com threads aguardando conexões...")


Código cliente.py

server_ip = input("Digite o IP do servidor: ")

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

client.connect((server_ip, 5678))

op = input("Digite a operação (add, subtract, multiply, divide): ")

num1 = input("Digite o primeiro número: ")

num2 = input("Digite o segundo número: ")

client.send(f"{op} {num1} {num2}".encode())

result = client.recv(1024).decode()

print("Resultado:", result)

client.close()



Tempo de resposta aproximado: 00:02:50 (2 segundos e 50 milissegundos)

Exemplo usado para teste de tempo:

Digite a operação (add, subtract, multiply, divide): add

Digite o primeiro número: 5

Digite o segundo número: 5

Resultado: 10.0


b) usando threads no cliente e no servidor.


Código servidor_threads.py:

import socket

import threading

import time


def handle_client(conn, addr):

    print(f"Conexão de {addr}")
    
    data = conn.recv(1024).decode()
    
    if not data:
    
        return
        
    try:
    
        op, num1, num2 = data.split()
        
        num1, num2 = float(num1), float(num2)
        

        # Simula tempo de processamento
        
        time.sleep(2)
        

        if op == "add":
        
            result = num1 + num2
            
        elif op == "subtract":
        
            result = num1 - num2
            
        elif op == "multiply":
        
            result = num1 * num2
            
        elif op == "divide":
        
            result = num1 / num2 if num2 != 0 else "Erro: Divisão por zero"
            
        else:
        
            result = "Erro: Operação inválida"
            


        conn.send(str(result).encode())
        

    except Exception as e:
    
        conn.send(f"Erro: {e}".encode())
        
    finally:
    
        conn.close()
        

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server.bind(("0.0.0.0", 5678))

server.listen(5)

print("Servidor com threads aguardando conexões...")


Código cliente_threads.py:

import time


server_ip = input("Digite o IP do servidor: ")


def fazer_requisicao(op, num1, num2):

    client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    
    client.connect((server_ip, 5678))
    
    client.send(f"{op} {num1} {num2}".encode())
    
    result = client.recv(1024).decode()
    
    print(f"Resultado: {result}")
    
    client.close()
    

# Lista de operações para enviar

operacoes = [

    ("add", 5, 3),
    
    ("subtract", 10, 2),
    
    ("multiply", 4, 7),
    
    ("divide", 20, 4),
    
    ("divide", 10, 0)
    
]


start_time = time.time()


threads = []

for op, num1, num2 in operacoes:

    t = threading.Thread(target=fazer_requisicao, args=(op, num1, num2))
    
    t.start()
    
    threads.append(t)
    

for t in threads:

    t.join()
    

end_time = time.time()

print(f"Tempo total: {end_time - start_time:.2f} segundos")

Tempo de resposta aproximado: 00:02:54 (2 segundos e 54 milissegundos)

Resultados: 

Resultado: 8.0

Resultado: 5.0

Resultado: 8.0

Resultado: 28.0

Resultado: Erro: Divisão por zero



