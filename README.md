# Atividade-Jogo-da-velha-


Atividade — Jogo da Velha com PyQt6
Exercício 1 — Mapeamento do projeto
Criação da janela
A janela principal é criada pela classe JanelaJogo, que herda de QWidget:


class JanelaJogo(QWidget):
   def __init__(self):
       super().__init__()
       self.setWindowTitle("Jogo da Velha com IA (Minimax)")
       self.painel_ia = PainelPensamentoIA()
       self.tabuleiro = [""] * 9
       self.botoes = []
       self.jogo_ativo = True
       self.dificuldade_atual = DIFICULDADE_PADRAO
       self.probabilidade_aleatoria = DIFICULDADES[DIFICULDADE_PADRAO]
       self.montar_interface()

No   init	são configuradas as informações iniciais do jogo e da interface.

Os 9 botões do tabuleiro
Os botões são criados dentro de montar_interface() usando um for:
       grade = QGridLayout()
       for i in range(9):
           botao = QPushButton("")
           botao.setFixedSize(80, 80)
           botao.setStyleSheet("font-size: 24px;")
           botao.clicked.connect(lambda _, i=i: self.jogada_humano(i))
           self.botoes.append(botao)
           grade.addWidget(botao, i // 3, i % 3)



O range(9) faz com que sejam criados os 9 botões do tabuleiro.
Função executada quando o jogador clica
Quando um botão é clicado, ele chama a função jogada_humano:
self.jogada_humano(i))
           self.botoes.append(botao)
           grade.addWidget(botao, i // 3, i % 3)
A função recebe a posição clicada, verifica se ela está vazia e coloca o X no tabuleiro.
Verificação de vitória
A função verificar_vencedor recebe o tabuleiro e verifica as combinações possíveis de vitória. Se encontrar três símbolos iguais, retorna o jogador que venceu. Caso contrário, retorna None.
       vencedor = verificar_vencedor(self.tabuleiro)
       if vencedor:
           self.jogo_ativo = False
           texto = "Você venceu!" if vencedor == JOGADOR_HUMANO else "A IA venceu!"
           QMessageBox.information(self, "Fim de jogo", texto)
           return True


       if tabuleiro_cheio(self.tabuleiro):
           self.jogo_ativo = False
           QMessageBox.information(self, "Fim de jogo", "Empate!")
           return True



Jogada da IA
A IA usa a função melhor_jogada, que utiliza o Minimax para analisar as posições disponíveis e escolher uma jogada.
   def turno_ia(self):
       melhor_posicao, avaliacoes = melhor_jogada(self.tabuleiro, JOGADOR_IA, JOGADOR_HUMANO)
       posicao_escolhida = self.escolher_jogada(melhor_posicao, avaliacoes)


       self.painel_ia.iniciar_analise()
       for indice, (pos, pontuacao) in enumerate(avaliacoes):
           atraso = ATRASO_ENTRE_ANALISES_MS * (indice + 1)
           QTimer.singleShot(
               atraso,
               lambda pos=pos, pontuacao=pontuacao: self.painel_ia.adicionar_avaliacao(pos, pontuacao)
           )

Reinício da partida
O botão de reinício chama a função reiniciar. Ela limpa o tabuleiro e deixa os botões disponíveis novamente.
   def reiniciar(self):
       self.tabuleiro = [""] * 9
       self.jogo_ativo = True
       self.label_status.setText("Sua vez (X)")
       for botao in self.botoes:
           botao.setText("")
           botao.setEnabled(True)
       self.painel_ia.reiniciar()




Exercício 2 — Modificar a interface
O título da janela foi alterado para:


class JanelaJogo(QWidget):
   def __init__(self):
       super().__init__()
       self.setWindowTitle("Jogo da Velha -  Minimax")

Foi criado um QLabel para mostrar de quem é a vez:


       self.label_status = QLabel("Sua vez (X)")
       layout_jogo.addWidget(self.label_status)

O texto desse mesmo componente pode ser alterado usando:

  self.label_status.setText(
           "Vez da IA"
       )
z
A diferença é que QLabel("Sua vez") cria um componente novo, enquanto setText() apenas muda o conteúdo de um componente que já existe.
Também foi adicionado o botão Nova partida:
       botao_nova_partida = QPushButton(
           "Nova partida"
       )

Esse botão chama a função reiniciar() para começar uma nova partida.

Exercício 3 — Placar
Foi adicionado um placar para guardar as vitórias do jogador, da IA e os empates:


       self.placar_jogador = 0
       self.placar_ia = 0
       self.placar_empates = 0

Quando o jogador vence, o contador é aumentado:
         if vencedor == JOGADOR_HUMANO:


               self.placar_jogador += 1


               self.label_status.setText(
                   "Você venceu!"
               )



O placar fica aparecendo durante as partidas, 
Ao clicar em Nova partida, somente o tabuleiro é limpo. O placar continua porque os contadores não são zerados. Eles permanecem enquanto o programa estiver aberto.
