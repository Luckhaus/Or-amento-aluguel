import csv

class Imovel:
    def __init__(self, tipo, quartos=1, garagem=False, criancas=False, vagas_estudio=0):
        self.tipo = tipo.lower()
        self.quartos = quartos
        self.garagem = garagem
        self.criancas = criancas
        self.vagas_estudio = vagas_estudio

    def calcular_aluguel(self):
        valor_base = 0

        if self.tipo == "apartamento":
            valor_base = 700
            if self.quartos == 2:
                valor_base += 200
            if not self.criancas:  # desconto de 5% sem crianças
                valor_base *= 0.95
            if self.garagem:
                valor_base += 300

        elif self.tipo == "casa":
            valor_base = 900
            if self.quartos == 2:
                valor_base += 250
            if self.garagem:
                valor_base += 300

        elif self.tipo == "estudio":
            valor_base = 1200
            if self.vagas_estudio > 0:
                if self.vagas_estudio >= 2:
                    valor_base += 250
                    if self.vagas_estudio > 2:
                        valor_base += (self.vagas_estudio - 2) * 60

        else:
            raise ValueError("Tipo de imóvel inválido! Use: apartamento, casa ou estudio.")

        return valor_base

    def gerar_contrato(self):
        contrato_total = 2000
        parcelas = 5
        valor_parcela = contrato_total / parcelas
        return contrato_total, valor_parcela


def gerar_csv(valor_mensal, valor_parcela):
    with open("orcamento_aluguel.csv", "w", newline="") as csvfile:
        writer = csv.writer(csvfile)
        writer.writerow(["Mês", "Valor Aluguel (R$)", "Parcela Contrato (R$)", "Total Mensal (R$)"])
        for mes in range(1, 13):
            total_mensal = valor_mensal + valor_parcela
            writer.writerow([mes, f"{valor_mensal:.2f}", f"{valor_parcela:.2f}", f"{total_mensal:.2f}"])
    print("Arquivo 'orcamento_aluguel.csv' gerado com sucesso!")


if __name__ == "__main__":
    print("=== Sistema de Orçamento de Aluguel - Imobiliária R.M ===")
    tipo = input("Tipo de imóvel (apartamento / casa / estudio): ").strip().lower()

    quartos = 1
    if tipo in ["apartamento", "casa"]:
        quartos = int(input("Quantidade de quartos (1 ou 2): "))

    garagem = False
    vagas_estudio = 0
    if tipo in ["apartamento", "casa"]:
        garagem = input("Deseja incluir garagem? (s/n): ").lower() == 's'
    elif tipo == "estudio":
        vagas_estudio = int(input("Quantas vagas de estacionamento deseja? (0, 2, 3...): "))

    criancas = True
    if tipo == "apartamento":
        criancas = input("Possui crianças? (s/n): ").lower() == 's'

    imovel = Imovel(tipo, quartos, garagem, criancas, vagas_estudio)
    valor_mensal = imovel.calcular_aluguel()
    contrato_total, valor_parcela = imovel.gerar_contrato()

    print("\n--- Orçamento Gerado ---")
    print(f"Tipo de imóvel: {tipo.capitalize()}")
    print(f"Valor mensal do aluguel: R$ {valor_mensal:.2f}")
    print(f"Valor total do contrato: R$ {contrato_total:.2f} (5x de R$ {valor_parcela:.2f})")

    gerar_csv(valor_mensal, valor_parcela)
