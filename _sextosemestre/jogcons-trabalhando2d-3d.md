---
layout: artigo
nome: Jogos Para Consoles - Trabalhando 2D e 3D
---

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
using System.Speech.Synthesis;
using System.IO;

namespace model3da_and_2d
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        Song musicaFundo;
        SpriteFont fonte;
        Model modelo3D;
        float anguloNave = 0;
        const string NOME_ARQUIVO = @"c:\temp\jogowil.txt";

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

            modelo3D = Content.Load<Model>("Ship_06A");
            musicaFundo = Content.Load<Song>("T1"); //MP3
            //var explosao = Content.Load<SoundEffect>("bomba"); //WAV
            //explosao.Play();
                        
            fonte = Content.Load<SpriteFont>("Arial");

            string conteudoArq = string.Empty;

            if(File.Exists(NOME_ARQUIVO))
                conteudoArq = File.ReadAllText(NOME_ARQUIVO);

            SpeechSynthesizer sintetizador = new SpeechSynthesizer();
            var vozes = sintetizador.GetInstalledVoices();
            sintetizador.SelectVoice(vozes[0].VoiceInfo.Name);

            sintetizador.SpeakAsync("Hello, my name is Skynet. Your last visit has in " + conteudoArq);

            MediaPlayer.Volume = .1f;
            MediaPlayer.Play(musicaFundo);
            //MediaPlayer.IsRepeating = true;
        }

        /// <summary>
        /// UnloadContent will be called once per game and is the place to unload
        /// all content.
        /// </summary>
        protected override void UnloadContent()
        {
            DateTime data = DateTime.Now;
            File.WriteAllText(NOME_ARQUIVO, data.ToString());
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

            anguloNave += .02f;

            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.Black);

            Matrix world = Matrix.CreateRotationY(anguloNave);
            Matrix projection = Matrix.CreatePerspectiveFieldOfView(MathHelper.ToRadians(45), GraphicsDevice.Viewport.AspectRatio, 1, 1000);
            Matrix view = Matrix.CreateLookAt(new Vector3(0, 4, 7), Vector3.Zero, Vector3.Up);

            foreach (ModelMesh malha in modelo3D.Meshes)
            {
                foreach(BasicEffect shaderBasico in malha.Effects)
                {
                    shaderBasico.World = world;
                    shaderBasico.Projection = projection;
                    shaderBasico.View = view;
                    shaderBasico.EnableDefaultLighting();
                }

                malha.Draw();
            }

            spriteBatch.Begin(
                SpriteSortMode.Immediate,
                BlendState.AlphaBlend,
                SamplerState.LinearClamp,
                DepthStencilState.Default,
                RasterizerState.CullCounterClockwise);
            spriteBatch.DrawString(fonte, "jojos bizarre world", new Vector2(30, 130), Color.Yellow);
            spriteBatch.End();


            base.Draw(gameTime);
        }
    }
}

{% endhighlight %}
