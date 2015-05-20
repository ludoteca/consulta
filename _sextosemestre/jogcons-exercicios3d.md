---
layout: artigo
nome: Jogos para Consoles - Exercícios de 3D
---

## Shuriken andando
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
        Model modelo3D; //Modelo
        float angulo = 0; //Ângulo
        Vector3 modelTranslation;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content"; 
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

            modelo3D = Content.Load<Model>("Shuriken_003_free"); //Carregar o Modelo
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



            if (Keyboard.GetState().IsKeyDown(Keys.A))
            {
                modelTranslation.X += -1;
            }
            if (Keyboard.GetState().IsKeyDown(Keys.S))
            {
                modelTranslation.Z += 1;
            }
            if (Keyboard.GetState().IsKeyDown(Keys.D))
            {
                modelTranslation.X += 1;
            }
            if (Keyboard.GetState().IsKeyDown(Keys.W))
            {
                modelTranslation.Z += -1;
            }


            angulo += .02f; // Atualizar Variável

            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            Matrix worldRot = 
                Matrix.CreateRotationY(angulo);

            Matrix worldTrans = Matrix.CreateTranslation(modelTranslation);

            Matrix view = Matrix.CreateLookAt(
                new Vector3(0, 30, 30),
                new Vector3(0, 0, 0),
                Vector3.Up);

            Matrix projection = Matrix.CreatePerspectiveFieldOfView(
                MathHelper.ToRadians(45),
                GraphicsDevice.Viewport.AspectRatio,
                1,
                1000);

            modelo3D.Draw(worldRot * worldTrans, view, projection);

            base.Draw(gameTime);
        }
    }
}

{% endhighlight %}

## Shuriken Shaders
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


            base.Draw(gameTime);
        }
    }
}

{% endhighlight %}
