# Meals App
### Search meals based on various way.

## Project Installations
<ul>
<li>Clone/download the repository.</li>
<li>Run <code>npm install</code></li>
<li>Copy <code>.env.example</code> to <code>.env</code></li>
<li>Run <code>npm run dev</code> to start the application <code>http://127.0.0.1:3000</code> or <code>http://localhost:3000</code></li>
</ul>

# Create the script file
vi setup-vue.sh

# this FULL script (one file only)
#!/bin/bash
set -e

echo "🔹 Updating system"
sudo apt update -y && sudo apt upgrade -y

echo "🔹 Installing Nginx"
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx

echo "🔹 Installing Node.js (LTS) and npm"
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs build-essential

echo "🔹 Checking Node & npm versions"
node -v
npm -v

echo "🔹 Installing Git"
sudo apt install git -y

echo "🔹 Cloning Vue project"
cd ~
git clone https://github.com/alamgirweb11/meals-app.git
mv meals-app vue-app
cd vue-app

echo "🔹 Creating .env file"
cat <<EOF > .env
VITE_API_BASE_URL=https://www.themealdb.com/api/json/v1/1/
EOF

echo "🔹 Installing dependencies"
npm install --legacy-peer-deps

echo "🔹 Building Vue app"
npm run build

echo "🔹 Creating web directory"
sudo mkdir -p /var/www/vue-app
sudo cp -r dist /var/www/vue-app/

echo "🔹 Configuring Nginx"
sudo tee /etc/nginx/sites-available/vue-app > /dev/null <<EOF
server {
    listen 80;
    server_name _;

    root /var/www/vue-app/dist;
    index index.html;

    location / {
        try_files \$uri \$uri/ /index.html;
    }
}
EOF

echo "🔹 Enabling Nginx config"
sudo rm -f /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/vue-app /etc/nginx/sites-enabled/

echo "🔹 Testing Nginx"
sudo nginx -t

echo "🔹 Restarting Nginx"
sudo systemctl restart nginx

echo "✅ Vue app deployed successfully!"
echo "🌍 Open your EC2 Public IP in browser"

# Give execute permission
chmod +x setup-vue.sh
# run this scriptfile
./setup-vue.sh
# Open in browser:
http://<EC2-PUBLIC-IP>
