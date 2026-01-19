# AI Operations Agent - Setup Guide

## Complete Installation Instructions

This guide will help you set up the AI Operations Agent system locally on your machine. Follow these steps carefully.

## Prerequisites

- **Node.js**: v16 or higher (Download from https://nodejs.org/)
- - **npm**: v8 or higher (comes with Node.js)
  - - **Git**: Download from https://git-scm.com/
    - - **MongoDB or PostgreSQL**: Choose your preferred database
      - - **Code Editor**: VS Code recommended (https://code.visualstudio.com/)
        - - **API Keys**:
          -   - OpenAI API Key (https://platform.openai.com/api-keys)
              -   - (Optional) Anthropic Claude API Key
               
                  - ## Step 1: Clone the Repository
               
                  - ```bash
                    git clone https://github.com/asemalkahtani/AI-Operations-Agent.git
                    cd AI-Operations-Agent
                    ```

                    ## Step 2: Backend Setup

                    ### 2.1 Install Backend Dependencies

                    ```bash
                    cd backend
                    npm install
                    ```

                    ### 2.2 Create Environment Variables

                    Create a `.env` file in the `backend` directory:

                    ```bash
                    cp .env.example .env
                    ```

                    Edit `.env` with your configuration:

                    ```
                    # Server Configuration
                    PORT=5000
                    NODE_ENV=development

                    # Database (Choose one)
                    # MongoDB
                    MONGODB_URI=mongodb://localhost:27017/ai-operations

                    # OR PostgreSQL
                    DATABASE_URL=postgresql://user:password@localhost:5432/ai-operations

                    # AI Services
                    OPENAI_API_KEY=your_openai_api_key_here
                    ANTHROPIC_API_KEY=your_anthropic_api_key_here

                    # JWT Configuration
                    JWT_SECRET=your_super_secret_jwt_key_min_32_chars
                    JWT_EXPIRE=7d

                    # Logging
                    LOG_LEVEL=debug
                    ```

                    ### 2.3 Start Backend Server

                    ```bash
                    npm run dev
                    ```

                    The server will run on `http://localhost:5000`

                    ## Step 3: Frontend Setup

                    ### 3.1 Install Frontend Dependencies

                    ```bash
                    cd frontend
                    npm install
                    ```

                    ### 3.2 Configure Frontend

                    Create `.env` file in `frontend` directory:

                    ```
                    VITE_API_URL=http://localhost:5000
                    VITE_APP_NAME=AI Operations Agent
                    ```

                    ### 3.3 Start Frontend Development Server

                    ```bash
                    npm run dev
                    ```

                    The frontend will be available at `http://localhost:5173`

                    ## Step 4: Database Setup

                    ### Using MongoDB

                    #### Install MongoDB Community Edition

                    **macOS (using Homebrew):**
                    ```bash
                    brew tap mongodb/brew
                    brew install mongodb-community
                    ```

                    **Windows:**
                    Download from https://www.mongodb.com/try/download/community

                    **Linux (Ubuntu):**
                    ```bash
                    wget -qO - https://www.mongodb.com/mongo-db-repo-distribution-keyring.gpg | sudo apt-key add -
                    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
                    sudo apt-get update
                    sudo apt-get install -y mongodb-org
                    ```

                    #### Start MongoDB

                    ```bash
                    # macOS
                    brew services start mongodb-community

                    # Linux
                    sudo systemctl start mongod

                    # Windows (if installed as service)
                    # It should start automatically
                    ```

                    Verify MongoDB is running:
                    ```bash
                    mongosh
                    ```

                    ### Using PostgreSQL

                    #### Install PostgreSQL

                    **macOS (using Homebrew):**
                    ```bash
                    brew install postgresql
                    brew services start postgresql
                    ```

                    **Windows:**
                    Download from https://www.postgresql.org/download/windows/

                    **Linux (Ubuntu):**
                    ```bash
                    sudo apt-get install postgresql postgresql-contrib
                    sudo systemctl start postgresql
                    ```

                    #### Create Database

                    ```bash
                    psql
                    CREATE DATABASE ai_operations;
                    \c ai_operations
                    ```

                    ## Step 5: Database Initialization (Optional)

                    If migrations are set up:

                    ```bash
                    cd backend
                    npm run migrate
                    npm run seed
                    ```

                    ## Step 6: Verify Installation

                    ### Check Backend

                    ```bash
                    curl http://localhost:5000/api/health
                    ```

                    Expected response:
                    ```json
                    {
                      "status": "healthy",
                      "timestamp": "2024-01-19T10:00:00Z"
                    }
                    ```

                    ### Access Frontend

                    Open browser and navigate to: `http://localhost:5173`

                    ## Troubleshooting

                    ### Issue: "Port 5000 already in use"

                    **Solution:**
                    ```bash
                    # Find process using port
                    lsof -i :5000

                    # Kill process
                    kill -9 <PID>

                    # Or use different port
                    PORT=5001 npm run dev
                    ```

                    ### Issue: "MongoDB connection failed"

                    **Solution:**
                    - Ensure MongoDB is running: `brew services list`
                    - - Check MONGODB_URI in `.env`
                      - - Verify MongoDB is accessible on localhost:27017
                       
                        - ### Issue: "npm command not found"
                       
                        - **Solution:**
                        - - Install Node.js from https://nodejs.org/
                          - - Restart your terminal
                            - - Verify: `node -v` and `npm -v`
                             
                              - ### Issue: "EACCES permission denied" on macOS/Linux
                             
                              - **Solution:**
                              - ```bash
                                # Fix npm permissions
                                sudo chown -R $(whoami) ~/.npm

                                # Or use nvm instead
                                curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
                                nvm install 18
                                nvm use 18
                                ```

                                ## Running Tests

                                ### Backend Tests

                                ```bash
                                cd backend
                                npm run test
                                ```

                                ### Frontend Tests

                                ```bash
                                cd frontend
                                npm run test
                                ```

                                ## Running with Docker

                                ### Prerequisites

                                - Docker installed (https://www.docker.com/products/docker-desktop)
                               
                                - ### Build and Run
                               
                                - ```bash
                                  docker-compose up -d
                                  ```

                                  Check services:
                                  ```bash
                                  docker-compose ps
                                  ```

                                  View logs:
                                  ```bash
                                  docker-compose logs -f
                                  ```

                                  Stop services:
                                  ```bash
                                  docker-compose down
                                  ```

                                  ## Development Workflow

                                  ### File Structure

                                  ```
                                  backend/
                                  ├── src/
                                  │   ├── agents/          # AI agent logic
                                  │   ├── controllers/     # Route handlers
                                  │   ├── models/          # Database schemas
                                  │   ├── routes/          # API routes
                                  │   ├── middleware/      # Express middleware
                                  │   ├── utils/           # Helper functions
                                  │   └── app.js           # Express app config
                                  ├── config/              # Configuration files
                                  ├── tests/               # Test files
                                  ├── .env.example         # Environment template
                                  └── package.json

                                  frontend/
                                  ├── src/
                                  │   ├── components/      # React components
                                  │   ├── pages/           # Page components
                                  │   ├── services/        # API services
                                  │   ├── store/           # State management
                                  │   ├── utils/           # Helper functions
                                  │   └── App.jsx          # Main app component
                                  ├── public/              # Static files
                                  ├── vite.config.js       # Vite configuration
                                  └── package.json
                                  ```

                                  ### Adding New Agents

                                  1. Create new agent file in `backend/src/agents/`
                                  2. 2. Implement agent logic
                                     3. 3. Register agent in agent manager
                                        4. 4. Add API endpoint for agent
                                          
                                           5. ### Adding New API Endpoints
                                          
                                           6. 1. Create controller in `backend/src/controllers/`
                                              2. 2. Create route in `backend/src/routes/`
                                                 3. 3. Import route in `backend/src/app.js`
                                                    4. 4. Test with curl or Postman
                                                      
                                                       5. ## API Documentation
                                                      
                                                       6. ### Health Check
                                                      
                                                       7. ```bash
                                                          GET /api/health
                                                          ```

                                                          ### Operations Endpoints

                                                          ```bash
                                                          # List all operations
                                                          GET /api/operations

                                                          # Create operation
                                                          POST /api/operations

                                                          # Get operation details
                                                          GET /api/operations/:id

                                                          # Update operation
                                                          PUT /api/operations/:id

                                                          # Delete operation
                                                          DELETE /api/operations/:id
                                                          ```

                                                          ### Agents Endpoints

                                                          ```bash
                                                          # List all agents
                                                          GET /api/agents

                                                          # Create agent
                                                          POST /api/agents

                                                          # Get agent details
                                                          GET /api/agents/:id
                                                          ```

                                                          ### Authentication

                                                          ```bash
                                                          # Register
                                                          POST /api/auth/register

                                                          # Login
                                                          POST /api/auth/login

                                                          # Refresh token
                                                          POST /api/auth/refresh
                                                          ```

                                                          ## Common Commands

                                                          ### Backend

                                                          ```bash
                                                          npm run dev          # Start development server
                                                          npm run start        # Start production server
                                                          npm run test         # Run tests
                                                          npm run lint         # Run linter
                                                          npm run format       # Format code
                                                          ```

                                                          ### Frontend

                                                          ```bash
                                                          npm run dev          # Start development server
                                                          npm run build        # Build for production
                                                          npm run preview      # Preview production build
                                                          npm run test         # Run tests
                                                          npm run lint         # Run linter
                                                          ```

                                                          ## Next Steps

                                                          1. **Configure AI Models**: Set up OpenAI/Anthropic API keys
                                                          2. 2. **Create First Agent**: Follow agent creation guide
                                                             3. 3. **Set Up Workflows**: Design operation workflows
                                                                4. 4. **Add Team Members**: Configure user access
                                                                   5. 5. **Deploy**: Choose hosting platform (AWS, Heroku, DigitalOcean)
                                                                     
                                                                      6. ## Support & Resources
                                                                     
                                                                      7. - Documentation: See README.md
                                                                         - - Issues: https://github.com/asemalkahtani/AI-Operations-Agent/issues
                                                                           - - Discussions: https://github.com/asemalkahtani/AI-Operations-Agent/discussions
                                                                            
                                                                             - ## Security Notes
                                                                            
                                                                             - - Never commit `.env` files
                                                                               - - Use strong JWT secrets
                                                                                 - - Keep dependencies updated: `npm update`
                                                                                   - - Use environment variables for sensitive data
                                                                                     - - Enable HTTPS in production
                                                                                      
                                                                                       - Happy coding!
