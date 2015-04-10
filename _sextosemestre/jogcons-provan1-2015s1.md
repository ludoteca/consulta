---
layout: artigo
nome: Jogos Para Consoles - 2015 S1 - Prova N1
---

# Jogos Para Consoles - Prova N1

## Questões

1-) [1 pt] Em uma tela de 512x512(em modo janela), desenhe o fundo do jogo utilizando a imagem fundo.jpg

2-) [1 pt] Desenhe a imagem tanque_corpo.png

3-) [1 pt] Permita que as setas (esquerda ou direita) do teclado gire o tanque sobre seu centro.

4-) [1 pt] Utilize funções trigonométricas para fazer o corpo do tanque avançar ou retroceder na direção cujo aponta utilizando as teclas para cima e baixo do teclado.

5-) [2,5 pts] Crie um scroll vertical que mova o fundo da tela para baixo apenas quando o tanque tocar a região aproximada da linha vermelha do exemplo.jpg, neste caso o tanque deve parar de subir.

	A-) O Tanque deve ter movimento livre enquanto não tocar a linha vermelha da tela.

6-) [0,5 pt] Crie um notificador de "Percusso percorrido" conforme exemplo acima, defina uma escala de distância a gosto, este placar deve aumentar quando o scroll rolar para baixo.

7-) [1 pt] Faça com o que o canhão ("tanque_canhao.png") acompanhe o corpo do tanque sempre centralizado pela região aproximada da escotilha.

8-) [2 pts] Permita que as teclas "A" e "D" girem o canhão ao redor da região aproximada da escotilha.


### Game1.cs 

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;

namespace WindowsGame1
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        Tanque tanque;
        Fundo fundo;
        SpriteFont fonte;
        int scrollDistance = 0;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
            graphics.PreferredBackBufferHeight = graphics.PreferredBackBufferWidth = 512;
        }

        /// <summary>
        /// Allows the game to perform any initialization it needs to before starting to run.
        /// This is where it can query for any required services and load any non-graphic
        /// related content.  Calling base.Initialize will enumerate through any components
        /// and initialize them as well.
        /// </summary>
        protected override void Initialize()
        {
            // TODO: Add your initialization logic here

            base.Initialize();
        }

        /// <summary>
        /// LoadContent will be called once per game and is the place to load
        /// all of your content.
        /// </summary>
        protected override void LoadContent()
        {
            // Create a new SpriteBatch, which can be used to draw textures.
            spriteBatch = new SpriteBatch(GraphicsDevice);
            tanque = new Tanque(Content);
            fundo = new Fundo(Content);
            fonte = Content.Load<SpriteFont>("fonte");
            
            // TODO: use this.Content to load your game content here
        }

        /// <summary>
        /// UnloadContent will be called once per game and is the place to unload
        /// all content.
        /// </summary>
        protected override void UnloadContent()
        {
            // TODO: Unload any non ContentManager content here
        }

        /// <summary>
        /// Allows the game to run logic such as updating the world,
        /// checking for collisions, gathering input, and playing audio.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Update(GameTime gameTime)
        {
            // Allows the game to exit
            if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed)
                this.Exit();

            // TODO: Add your update logic here
            if (tanque.VerificaPosicaoTanque()){
                fundo.AtivarScroll();
                scrollDistance++;
            } else {
                fundo.DesativarScroll();
            }


            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            // TODO: Add your drawing code here
            spriteBatch.Begin();
            fundo.Desenhar(spriteBatch);
            tanque.Desenhar(spriteBatch);
            spriteBatch.DrawString(fonte, "Percurso percorrido: " + scrollDistance.ToString(), new Vector2(10, 10), Color.White);
            spriteBatch.End();
            base.Draw(gameTime);
        }
    }
}

{% endhighlight %}

### Tanque.cs 

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;

namespace WindowsGame1
{
    class Tanque
    {
        Texture2D corpo, canhao;
        Vector2 posicaoTanque, posicaoCanhao, direcao;
        const float angulo = 0.05f;
        float rotacaoTanque, rotacaoCanhao, velocidade = 2.0f;
        int posicaoLinha = 150;
        

        public Tanque(ContentManager Content){
            corpo = Content.Load<Texture2D>("tanque_corpo");
            canhao = Content.Load<Texture2D>("tanque_canhao");
            posicaoTanque = new Vector2(300, 300);
            direcao = new Vector2(0, 0);
            // posicionando o canhao no local correto
            PosicionaCanhao();
        }

        void PosicionaCanhao()
        {
            posicaoCanhao = posicaoTanque;
            posicaoCanhao.X += direcao.X;
            posicaoCanhao.Y += direcao.Y;
        }

        void Atualizar(){

            direcao.X = (float) Math.Cos(rotacaoTanque);
            direcao.Y = (float) Math.Sin(rotacaoTanque);
            velocidade = 0;

            if(Keyboard.GetState().IsKeyDown(Keys.Left)){
                GiraTanqueEsquerda();
            }

            if(Keyboard.GetState().IsKeyDown(Keys.Right)){
                GiraTanqueDireita();
            }

            if (Keyboard.GetState().IsKeyDown(Keys.Up))
            {
                AndaTanqueFrente();
            }

            if (Keyboard.GetState().IsKeyDown(Keys.Down))
            {
                AndaTanqueTras();
            }

            if (Keyboard.GetState().IsKeyDown(Keys.A))
            {
                GiraCanhaoEsquerda();
            }
            if (Keyboard.GetState().IsKeyDown(Keys.D))
            {
                GiraCanhaoDireita();
            }

            posicaoTanque.X +=  direcao.X * velocidade;
            posicaoTanque.Y +=  direcao.Y * velocidade;
            PosicionaCanhao();
        }

        public bool VerificaPosicaoTanque()
        {
            if (posicaoTanque.Y <= posicaoLinha){
                posicaoTanque.Y++; //Para que o jogador fique segurando para que o scroll funcione
                return true;
            }

            return false;
        }

        #region Movimentação do Tanque
        void GiraTanqueEsquerda()
        {
            rotacaoTanque -= angulo;
        }

        void GiraTanqueDireita()
        {
            rotacaoTanque += angulo;
        }

        void AndaTanqueFrente()
        {
            velocidade++;
        }

        void AndaTanqueTras()
        {
            velocidade--;
        }

        void GiraCanhaoEsquerda()
        {
            rotacaoCanhao -= angulo;
        }

        void GiraCanhaoDireita()
        {
            rotacaoCanhao += angulo;
        }
        #endregion

        public void Desenhar(SpriteBatch sb){
            Atualizar();
            sb.Draw(corpo, posicaoTanque, new Rectangle(0, 0, 107, 53), Color.White, rotacaoTanque, new Vector2(corpo.Width/2, corpo.Height/2), 1, SpriteEffects.None, 0);
            sb.Draw(canhao, posicaoCanhao, new Rectangle(0, 0, 117, 48), Color.White, rotacaoCanhao, new Vector2(canhao.Width * 0.4f, canhao.Height * 0.5f), 1, SpriteEffects.None, 0);
        }

    }
}

{% endhighlight %}

### Fundo.cs 

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;


namespace WindowsGame1
{
    class Fundo
    {
        Texture2D sprite;
        int posicao;
        const int velocidade = 2;
        bool scroll;

        public Fundo(ContentManager Content)
        {
            sprite = Content.Load<Texture2D>("fundo");
        }

        public void AtivarScroll() { scroll = true; }

        public void DesativarScroll() { scroll = false; }

        void Atualizar()
        {
            if (scroll)
            {
                posicao += velocidade;
                if (posicao >= 512)
                {
                    posicao = 0;
                }
            }
        }

        public void Desenhar(SpriteBatch sb)
        {
            Atualizar();
            sb.Draw(sprite, new Vector2(0, posicao), Color.White);
            sb.Draw(sprite, new Vector2(0, posicao - sprite.Height), Color.White);
        }
    }
}

{% endhighlight %}