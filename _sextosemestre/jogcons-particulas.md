---
layout: artigo
nome: Jogos Para Consoles - Partículas
---

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

namespace Particulas
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        Texture2D imgFogo;
        Texture2D imgFumaca;
        List<Particula> particulas;
        Texture2D imgCursor;
        static Random sorteador;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
            graphics.PreferredBackBufferWidth = 1024;
            graphics.PreferredBackBufferHeight = 768;
            //graphics.IsFullScreen = true;
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

            imgFogo = Content.Load<Texture2D>("explosion");
            imgFumaca = Content.Load<Texture2D>("smoke");
            imgCursor = Content.Load<Texture2D>("cursor");

            particulas = new List<Particula>();

            sorteador = new Random();

            Particula.LimitesTela = GraphicsDevice.Viewport.Bounds;
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

            if (Mouse.GetState().LeftButton == ButtonState.Pressed)
                CriaOuReciclaParticula();

            foreach (var particula in particulas)
                particula.Atualiza();

            base.Update(gameTime);
        }

        private void CriaOuReciclaParticula()
        {
            Particula novaParticula = null;
            bool particulaReciclada = false;

            foreach (var particula in particulas)
            {
                if (!particula.Visivel)
                {
                    novaParticula = particula;
                    novaParticula.Inicializa();
                    novaParticula.Visivel = true;
                    particulaReciclada = true;
                    break;
                }
            }

            if (!particulaReciclada)
                novaParticula = new Particula();

            novaParticula.RotacaoDireita = (sorteador.Next(1, 10) > 5);
            novaParticula.Tipo = (sorteador.Next(1, 10) > 7) ? TipoParticula.Fogo : TipoParticula.Fumaca;
            novaParticula.Visivel = true;
            novaParticula.Posicao = new Vector2(Mouse.GetState().X, Mouse.GetState().Y);
            novaParticula.Velocidade = sorteador.Next(1, 5);

            if (!particulaReciclada)
                particulas.Add(novaParticula);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.Black);

            spriteBatch.Begin(SpriteSortMode.Immediate, BlendState.Additive, SamplerState.LinearClamp, DepthStencilState.Default, RasterizerState.CullCounterClockwise);
            //spriteBatch.Begin();

            foreach (var particula in particulas)
                particula.Desenha(spriteBatch, particula.Tipo == TipoParticula.Fogo ? imgFogo : imgFumaca);


            spriteBatch.Draw(imgCursor, new Vector2(Mouse.GetState().X, Mouse.GetState().Y), Color.White);

            spriteBatch.End();

            base.Draw(gameTime);
        }
    }
}

{% endhighlight %}

### Particula.cs
{% highlight csharp %}
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;

namespace Particulas
{
    public class Particula
    {
        public static Rectangle LimitesTela { get; set; }
        public float Velocidade {get; set;}
        public Vector2 Posicao;
        public float Direcao { get; set; }
        public float Tamanho { get; set; }
        public bool Visivel { get; set; }
        public float Angulo { get; set; }
        public Vector2 Origem;
        public bool RotacaoDireita;
        public TipoParticula Tipo { get; set; }
        public float Opacidade;

        public Particula()
        {
            Inicializa();
        }

        public void Inicializa()
        {
            Opacidade = 1f;
            Tamanho = .1f;
            Tipo = TipoParticula.Fogo;
        }

        public void Atualiza()
        {
            if (!Visivel)
                return;

            //Posicao = new Vector2(Posicao.X + Velocidade, Posicao.Y + Velocidade);

            if (Posicao.X < 0 || 
                Posicao.X > LimitesTela.Width ||
                Posicao.Y < 0 ||
                Posicao.Y > LimitesTela.Height)
                    Visivel = false;

            Tamanho += .05f;

            if (RotacaoDireita)
                Angulo += .02f;
            else
                Angulo -= .02f;

            Opacidade -= .01f;

            if (Opacidade <= 0) Visivel = false;
        }

        public void Desenha(SpriteBatch spriteBatch, Texture2D imagem)
        {
            if (Visivel)
            {
                if (Origem.X == 0)
                    Origem = new Vector2(imagem.Width / 2, imagem.Height / 2);

                spriteBatch.Draw(imagem, Posicao, null, new Color(1,1,1,Opacidade), Angulo, Origem, Tamanho, SpriteEffects.None, 0);
            }
        }
    }

    public enum TipoParticula
    {
        Fogo,
        Fumaca
    }
}

{% endhighlight %}
