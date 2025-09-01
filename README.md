# t1
import requests
import time

# Lista de nomes das moléculas que você quer baixar
# Colocar um selecionador pelo nome 
nom= input("Digite o nome da molécula que deseja baixar: ")
name_list = [nom]  # substitua com seus nomes

for name in name_list:
    # URL para a API PUG REST
    url = f'https://pubchem.ncbi.nlm.nih.gov/rest/pug/compound/name/{name}/record/SDF?record_type=3d'

    # Faz a requisição para o PubChem
    response = requests.get(url)

    # Verifica se a requisição foi bem sucedida
    if response.status_code == 200:
        #mostrar cids
        cids= response.text.strip().split("\n") 
        if not cids or cids == ['']:
            print(f'Nenhum CID encontrado para {name}')
            exit() 
            print("\nResultados encontrados:")
    for i, cid in enumerate(cids[:5], start=1):
        try:
        # Buscar o nome do composto para mostrar junto
            info_url = f"https://pubchem.ncbi.nlm.nih.gov/rest/pug/compound/cid/{cid}/property/IUPACName,MolecularFormula,MolecularWeight/JSON"
            info_resp = requests.get(info_url).json()

         
            dados = info_resp['PropertyTable']["Properties"][0]
            formula = dados.get("MolecularFormula", "Sem fórmula")
            peso= dados.get("MolecularWeight", "Sem peso")
            iupac = dados.get("IUPACName", "Sem nome")
            print(f'{i}. CID {cid} - {formula} - {peso:.2f} g/mol - {iupac}') 
        except Exception:
            print(f"{i}. CID {cid} - informações não disponíveis")

    else:
        print('Erro na busca')
    
    
        #data = response.json()
        #cids = [compound['id']['cid'] for compound in data['PC_Compounds']]
        #print(f'CIDs encontrados para {name}: {cids}')
        # Salva o resultado em um arquivo .sdf
        #with open(f'{name}.sdf', 'w') as f:
            #f.write(response.text)
    #else:
        #glutaprint(f'Erro ao baixar {name}: {response.status_code}')

    # Pausa entre as requisições para evitar sobrecarregar o servidor do PubChem
    time.sleep(1)
