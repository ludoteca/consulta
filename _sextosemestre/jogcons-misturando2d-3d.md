---
layout: artigo
nome: Jogos Para Consoles - Misturando 2D com 3D
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

namespace Trabalhando3D
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        SpriteFont fonte;
        Model modelo3D;
        float angulo = 0;
        Vector3 posicao;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
            graphics.PreferredBackBufferWidth = 1024;
            graphics.PreferredBackBufferHeight = 768;
            graphics.IsFullScreen = true;
            
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

            modelo3D = Content.Load<Model>("Ship");
            fonte = Content.Load<SpriteFont>("spritez");
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

            KeyboardState kbs = Keyboard.GetState();
            if (kbs.IsKeyDown(Keys.Left))
                posicao.X--;
            if (kbs.IsKeyDown(Keys.Right))
                posicao.X++;
            if (kbs.IsKeyDown(Keys.Up))
                posicao.Z--;
            if (kbs.IsKeyDown(Keys.Down))
                posicao.Z++;
            if (kbs.IsKeyDown(Keys.Escape))
                this.Exit();


            angulo += .02f;

            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.Black);

            Matrix world =
                Matrix.CreateRotationY(angulo) *
                Matrix.CreateTranslation(posicao);

            Matrix view = Matrix.CreateLookAt(
                new Vector3(0, 3, 6),
                new Vector3(0, 0, 0),
                Vector3.Up);

            Matrix projection = Matrix.CreatePerspectiveFieldOfView(
                MathHelper.ToRadians(45),
                GraphicsDevice.Viewport.AspectRatio,
                1,
                1000);

            foreach (var malha in modelo3D.Meshes)
            {
                foreach (BasicEffect efeitoShader in malha.Effects)
                {
                    efeitoShader.World = world;
                    efeitoShader.View = view;
                    efeitoShader.Projection = projection;
                    efeitoShader.EnableDefaultLighting();
                    efeitoShader.FogEnabled = true;
                    efeitoShader.FogColor = Color.Black.ToVector3();
                    efeitoShader.FogStart = 3f;
                    efeitoShader.FogEnd = 12f;
                }
                malha.Draw();
            }

     
            spriteBatch.Begin();
            spriteBatch.DrawString(fonte, "atssrrtreaaASADFFSDGRGRTHRT", Vector2.One * 200, Color.White);
            spriteBatch.End();

            GraphicsDevice.BlendState = BlendState.Opaque;
            GraphicsDevice.DepthStencilState = DepthStencilState.Default;
            //GraphicsDevice.SamplerStates[0] = SamplerState.LinearWrap;
            base.Draw(gameTime);
    
        }
    }
}

{% endhighlight %}
