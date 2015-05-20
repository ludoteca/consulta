---
layout: artigo
nome: Atividade 3D XNA
---

## Atividade do Atrium Sponza

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
        Model modeloSponza;

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
            modeloSponza = Content.Load<Model>("sponza");   

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

            base.Update(gameTime);
        }


        float rot = 0;
        static Vector3 CONST_CAMPOS = new Vector3(0, 0, 7); //Posição Inicial
        static Vector3 CONST_CAMTAR = new Vector3(0, 0, 0); //Posição Inicial
        Vector3 camPos = CONST_CAMPOS;
        Vector3 camTar = CONST_CAMTAR;
        Vector3 camUp = Vector3.Up;

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            AtualizaPosicao();
            GraphicsDevice.Clear(Color.CornflowerBlue);
            
            float fov = MathHelper.ToRadians(45);
            if (camera) { rot += 0.01f; }
            Matrix world = Matrix.CreateRotationZ(rot) * Matrix.CreateRotationX((float)Math.PI / 2);
            Matrix view = Matrix.CreateLookAt(camPos, camTar, camUp);
            Matrix proj = Matrix.CreatePerspectiveFieldOfView(fov, GraphicsDevice.Viewport.AspectRatio, 1, 1000);

            foreach (var mesh in modeloSponza.Meshes){
                foreach (BasicEffect be in mesh.Effects) {
                    be.World = world;
                    be.Projection = proj;
                    be.View = view;
                    be.EnableDefaultLighting();
                }
                mesh.Draw();
            }

            base.Draw(gameTime);
        }

        bool camera = false;
            
        void MudaCamera(){
            if (camera){
                //Volta pra posição original porque sim
                camPos = CONST_CAMPOS;
                camTar = CONST_CAMTAR;
                rot = 0;
                camera = false;
            }
            else { 
                camPos = new Vector3(50,50,50);
                camera = true;
            } 
        }

        void AtualizaPosicao() {
            if (!camera)
            {
                if (Keyboard.GetState().IsKeyDown(Keys.Left))
                {
                    camPos.X--;
                    camTar.X--;
                }

                if (Keyboard.GetState().IsKeyDown(Keys.Right))
                {
                    camPos.X++;
                    camTar.X++;
                }

                if (Keyboard.GetState().IsKeyDown(Keys.Z))
                {
                    camPos.Y--;
                    camTar.Y--;
                }

                if (Keyboard.GetState().IsKeyDown(Keys.A))
                {
                    camPos.Y++;
                    camTar.Y++;
                }

                if (Keyboard.GetState().IsKeyDown(Keys.Down))
                {
                    camPos.Z++;
                    camTar.Z++;
                }


                if (Keyboard.GetState().IsKeyDown(Keys.Up))
                {
                    camPos.Z--;
                    camTar.Z--;
                }

                
            }
            if (Keyboard.GetState().IsKeyDown(Keys.Space)) MudaCamera();
        }
    }
}
{% endhighlight %}
