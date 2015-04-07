---
layout: artigo
nome: Jogos Para Consoles - Nave 2D com XNA
---
# Programa da nave completo
<span class="text-muted">Com alguns comentários</span>

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
using Espaco;

namespace Nave
{
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        Fundo fundo1;
        Fundo fundo2;
        Fundo fundo3;
        Espaco.Nave nave;
        SpriteFont fonte;
        Song som;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";

            //Define tela de 800x600
            graphics.PreferredBackBufferWidth = 800;
            graphics.PreferredBackBufferHeight = 600;
            //Tela cheia!
            //graphics.IsFullScreen = true;
        }

        protected override void Initialize()
        {
            base.Initialize();
        }

        protected override void LoadContent()
        {
            spriteBatch = new SpriteBatch(GraphicsDevice);

            fundo1 = new Fundo(Content, "fundo", 2);
            fundo2 = new Fundo(Content, "nebula1", 4);
            fundo3 = new Fundo(Content, "nebula2", 6);

            nave = new Espaco.Nave(Content, 4, GraphicsDevice.Viewport.Bounds);

            fonte = Content.Load<SpriteFont>("fonte");
            som = Content.Load<Song>("trilha");
            MediaPlayer.IsRepeating = true;
            MediaPlayer.Play(som);
        }

        protected override void Update(GameTime gameTime)
        {
            fundo1.Atualizar();
            fundo2.Atualizar();
            fundo3.Atualizar();

            nave.Atualizar();

            KeyboardState estadoTeclado = Keyboard.GetState();
            if (estadoTeclado.IsKeyDown(Keys.Left)) nave.Volta();
            if (estadoTeclado.IsKeyDown(Keys.Right)) nave.Avanca();

            if (estadoTeclado.IsKeyDown(Keys.Up))
                nave.Sobe();
            else if (estadoTeclado.IsKeyDown(Keys.Down))
                nave.Desce();
            else
                nave.PosicaoNormal();


            if(estadoTeclado.IsKeyDown(Keys.Space))
                nave.PuxarGatilho();
            else
                nave.SoltarGatilho();


            if (estadoTeclado.IsKeyDown(Keys.Escape))
                this.Exit();

            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            spriteBatch.Begin();
            fundo1.Desenhar(spriteBatch);
            fundo2.Desenhar(spriteBatch);
            nave.Desenhar(spriteBatch);
            fundo3.Desenhar(spriteBatch);

            var posicaoTitulo = new Vector2(7, 7 + 7 * (float)Math.Sin(gameTime.TotalGameTime.TotalSeconds*5));
            spriteBatch.DrawString(fonte, "Simulado Prof. Willians", posicaoTitulo - (Vector2.One * 3), Color.Black);
            spriteBatch.DrawString(fonte, "Simulado Prof. Willians", posicaoTitulo, Color.Green);
            var texto = string.Format("Posicao X:{0}, Y:{1}", nave.Posicao.X.ToString("0000"), nave.Posicao.Y.ToString("0000"));
            var dimensoesTexto = fonte.MeasureString(texto);
            var posicaoNaTela = new Vector2(GraphicsDevice.Viewport.Width - dimensoesTexto.X, GraphicsDevice.Viewport.Height - dimensoesTexto.Y);
            spriteBatch.DrawString(fonte, texto, posicaoNaTela-(Vector2.One * 3), Color.Black);
            spriteBatch.DrawString(fonte, texto, posicaoNaTela, Color.Yellow);
            spriteBatch.End();

            base.Draw(gameTime);
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
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;

namespace Espaco
{
    public class Fundo
    {
        #region [ Campos privados ]
        private Texture2D imagem;
        private int x;
        #endregion

        #region [ Campos públicos ]
        public int Velocidade;
        #endregion

        #region [ Métodos públicos ]
        public Fundo(ContentManager Content, string assetName, int velocidadeScroll)
        {
            imagem = Content.Load<Texture2D>(assetName);
            Velocidade = velocidadeScroll;
        }

        public void Atualizar()
        {
            //Move fundo para esquerda da tela
            x -= Velocidade;
        }

        public void Desenhar(SpriteBatch spriteBatch)
        {
            //Desenha a primeira imagem
            spriteBatch.Draw(imagem, new Vector2(x, 0),Color.White);
            
            //Calcula o x da segunda imagem em relacao a primeira
            int x2 = x + imagem.Width;

            //Desenha a segunda imagem
            spriteBatch.Draw(imagem, new Vector2(x2, 0), Color.White);
            
            //Verifica se a segunda imagem assumiu a posicao inicial da primeira imagem e volta a primeira imagem ao seu lugar inicial.
            if (x2 <= 0) x = 0;
        }
        #endregion
    }
}
{% endhighlight %}

### Nave.cs

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;

namespace Espaco
{
    public class Nave
    {
        #region [ Campos públicos ]
        public Vector2 Posicao;
        public int Velocidade;
        public int larguraNave;
        public int alturaNave;
        #endregion

        #region [ Campos privados ]
        private Texture2D imagem;
        private Texture2D imagemProjetil;
        private SoundEffect somDisparo;
        private List<Projetil> projeteis;
        private Rectangle recorte;        
        private int indiceXAnimacao;
        private int indiceYAnimacao = 1;
        private int frame = 0;
        private int posicaoKeyFrame = 5;
        private int direcaoAnimacao = 1;
        private bool disparando = false;
        private Rectangle dimensoesTela;
        private float aceleracaoX;
        private float aceleracaoY;
        private float impulso = .3f;
        private const int NUMERO_PROJETEIS = 50;
        #endregion

        #region [ Métodos públicos ]
        public Nave(ContentManager Content, int velocidadeNave, Rectangle dTela)
        {
            dimensoesTela = dTela;
            Velocidade = velocidadeNave;

            string assetName = "naveSprite";
            string assetNameProjetil = "projetil";
            string assetSom = "silenciador";

            imagem = Content.Load<Texture2D>(assetName);

            imagemProjetil = Content.Load<Texture2D>(assetNameProjetil);

            somDisparo = Content.Load<SoundEffect>(assetSom);

            larguraNave = imagem.Width / 3;
            alturaNave = imagem.Height / 3;

            projeteis = new List<Projetil>();
            for (int i = 0; i < NUMERO_PROJETEIS; i++)
            {
                projeteis.Add(new Projetil());
            }
        }

        public void Sobe()
        {
            indiceYAnimacao = 0;
            aceleracaoY -= impulso;
        }

        public void Desce()
        {
            indiceYAnimacao = 2;
            aceleracaoY += impulso;
        }

        public void Avanca()
        {
            Posicao.X += Velocidade;
            aceleracaoX += impulso;
        }

        public void Volta()
        {
            Posicao.X -= Velocidade;
            aceleracaoX -= impulso;
        }

        public void PosicaoNormal()
        {
            indiceYAnimacao = 1;
        }

        public void PuxarGatilho()
        {
            disparando = true;
        }

        public void SoltarGatilho()
        {
            disparando = false;
        }

        public void Atualizar()
        {
            frame++;
            frame = frame % posicaoKeyFrame;

            //Desacelera a nave
            aceleracaoX *= .96f;
            aceleracaoY *= .96f;

            Posicao.X += aceleracaoX;
            Posicao.Y += aceleracaoY;

            if (frame == 0)
            {
                indiceXAnimacao += direcaoAnimacao;

                if (indiceXAnimacao == 0 || indiceXAnimacao == 2)
                    direcaoAnimacao *= -1;
            }


            //Define qual das naves sera recortada para o desenho
            recorte = new Rectangle(larguraNave * indiceXAnimacao, alturaNave * indiceYAnimacao, larguraNave, alturaNave);
        }

        public void Desenhar(SpriteBatch spriteBatch)
        {
            //Cria a cada tiro a cada "frame" milesimos de segundo.
            if (disparando && frame == 0)
            {
                foreach (Projetil projetil in projeteis)
                {
                    //Reaproveita os tiros que não sao mais uteis
                    if(!projetil.Visivel){

                        projetil.Visivel = true;

                        //Posiciona o tiro a frente da nave
                        projetil.Posicao = new Vector2(
                            Posicao.X + larguraNave, 
                            Posicao.Y + (alturaNave - imagemProjetil.Height)/2);

                        //Toca o som do tiro
                        somDisparo.Play();

                        break;
                    }
                }
            }

            //Desenha cada tiro VISIVEL!
            foreach (Projetil projetil in projeteis)
            {
                if (projetil.Visivel)
                {
                    spriteBatch.Draw(imagemProjetil, projetil.Posicao, Color.White);
                    projetil.Posicao.X += Projetil.Velocidade;

                    if (projetil.Posicao.X > dimensoesTela.Width)
                        projetil.Visivel = false;
                }
            }

            //Desenha a nave conforme variavel "recorte"
            spriteBatch.Draw(imagem, Posicao, recorte, Color.White);

        }

        #endregion
    }
}

{% endhighlight %}

### Projetil.cs

{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework;

namespace Espaco
{
    public class Projetil
    {
        public static int Velocidade = 10;
        public Vector2 Posicao;
        public bool Visivel;
    }
}

{% endhighlight %}
