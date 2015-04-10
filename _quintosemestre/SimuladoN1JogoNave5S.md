---
layout : artigo
nome : Simulado N1 - Jogo Nave 2015 5s
---

# Jogo Nave - Simulado N1 Jogos Web 2015

{% highlight html %}

<meta charset='utf8'>
<html>
    <head>
        <title>Vista de Simulação de Prova JDW</title>
        <script>
        var canvas, context;
        var imgFundo, imgModulo, imgDisco;
        var TECLA_ESQUERDA = 37;
        var TECLA_CIMA = 38;
        var TECLA_DIREITA = 39;
        var ALTURA_CHAO = 550;
        var EXTREMO_ESQUERDO_LUA = 366;
        var EXTREMO_DIREITO_LUA = 577;
        var teclaEsquerdaPressionada = false;
        var teclaCimaPressionada = false;
        var teclaDireitaPressionada = false;
        var impulsoHorizontal = 0;
        var pousouFrenteALua = false;
        var pousouForaDaLua = false;
        var emFrenteALua = false;
        var noChao = false;
        var naveForaDaTela = false;
        var fimDeJogo = false;
        var velocidade = 6;
        var velocidadeY = 4;
        var aceleracaoY = 0;
        var combustivel = 100;
        var altitude = 0;
        var discoVisivel = true;        
        
        var posicao =
        {
            x:0,
            y:0
        };
        
        var posicaoDisco =
        {
            x:250,
            y:100        
        };
        
        function Iniciar(){
            canvas = document.getElementById('canvas');
            context = canvas.getContext('2d');
            
            imgFundo = new Image();
            imgFundo.src='background.png';
            
            imgModulo = new Image();
            imgModulo.src='modulo.png';

            imgDisco = new Image();
            imgDisco.src='disco.png';                    
            
            requestAnimationFrame(Loop);
        }
        
        function Loop(){
            
            if(!fimDeJogo)        
                Atualizar();
                
            Desenhar();
            
            requestAnimationFrame(Loop);
        }
        
        function Atualizar(){
            if(teclaEsquerdaPressionada)
                impulsoHorizontal -= .1;
        
            if(teclaDireitaPressionada)
                impulsoHorizontal += .1;
            
            if(teclaCimaPressionada){
                aceleracaoY = 0;
                velocidadeY = 4;
                posicao.y -= velocidade;
                combustivel -= .05;
            }
            else{
                aceleracaoY += .001;
            }

            altitude = parseInt(ALTURA_CHAO - posicao.y);
            
            noChao = (altitude <= 0);
            emFrenteALua = posicao.x > EXTREMO_ESQUERDO_LUA &&
                (posicao.x + imgModulo.width) < EXTREMO_DIREITO_LUA;
                
            pousouFrenteALua =  noChao && emFrenteALua;                
            pousouForaDaLua =  noChao && !emFrenteALua;
            
            naveForaDaTela =
                posicao.x < 0 ||
                (posicao.x + imgModulo.width)  > canvas.width ||
                (posicao.y + imgModulo.height) < 0;
            
            if(!noChao){
                posicao.y += velocidadeY;
                velocidadeY += aceleracaoY;
                posicao.x += impulsoHorizontal;
                combustivel -= .01;
            }
            else{
                fimDeJogo = true;
            }
            
            posicaoDisco.x += 5;
            
            if(posicaoDisco.x > canvas.width){
                posicaoDisco.x = -imgDisco.width;
                posicaoDisco.y = Math.random() * (ALTURA_CHAO / 2);
            }
        }
        
        function Desenhar(){
            context.drawImage(imgFundo, 0, 0);
            context.drawImage(imgModulo, posicao.x, posicao.y);
            context.font = '30px Arial';
            context.fillStyle = 'yellow';
            context.fillText('Altura ' + altitude, 700,50);
            context.fillText('Combustivel ' + parseInt(combustivel), 615,100);
            
            context.font = '72px Arial';
            if(pousouFrenteALua){
                context.fillText('Click! Você ganhou!', 100, 300);
            }else if(pousouForaDaLua){
                context.fillText('Você perdeu!', 100, 300);
            }else if (naveForaDaTela){
                fimDeJogo = true;
                context.fillText('Você se perdeu!!!', 100, 300);            
            }else if (combustivel <= 0)
                context.fillText('Você perdeu!!!', 100, 300);                        
                
            if(discoVisivel)
                context.drawImage(imgDisco, posicaoDisco.x, posicaoDisco.y);
                
            if(HouveColisao())
                discoVisivel = false;                
        }
        
        function HouveColisao()
        {
            var c1 = posicao.x - posicaoDisco.x;
            var c2 = posicao.y - posicaoDisco.y;
            var h = Math.sqrt(c1*c1, c2*c2);
            context.fillText('hipotenusa' + h, 100, 300);                        
            
            return false;
        }
        
        function VerificaTeclaPressionada(event){
            if(event.keyCode == TECLA_ESQUERDA)
                teclaEsquerdaPressionada = true;
                
            if(event.keyCode == TECLA_DIREITA)
                teclaDireitaPressionada = true;                
                
            if(event.keyCode == TECLA_CIMA)
                teclaCimaPressionada = true;                                
        }
        
        function VerificaTeclaSolta(event){
            if(event.keyCode == TECLA_ESQUERDA)
                teclaEsquerdaPressionada = false;
                
            if(event.keyCode == TECLA_DIREITA)
                teclaDireitaPressionada = false;                
                
            if(event.keyCode == TECLA_CIMA)
                teclaCimaPressionada = false;        
        }
        </script>
    </head>
    <body onload='Iniciar();' onkeydown='VerificaTeclaPressionada(event);' onkeyup='VerificaTeclaSolta(event);'>
        <canvas id='canvas' width='900' height='700'>
        Seu browser não suporta canvas!
        </canvas>
    </body>
</html>


{% endhighlight html %}
