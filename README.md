# 🐘 PostgreSQL + pgAdmin4 Setup on NixOS

This guide explains how to install and configure **PostgreSQL** and **pgAdmin4** on **NixOS**, using your system configuration.

---

## 📦 1. Install PostgreSQL and pgAdmin4

Add the following to `/etc/nixos/configuration.nix`:

```nix
{
  environment.systemPackages = with pkgs; [
    postgresql    # PostgreSQL CLI tools
    pgadmin4      # Web UI for managing PostgreSQL databases
  ];

  services.postgresql = {
    enable = true;
    package = pkgs.postgresql_15;
  };

  systemd.services.pgadmin4 = {
    enable = true;
    description = "pgAdmin4 Web Application";
    after = [ "network.target" "postgresql.service" ];
    wantedBy = [ "multi-user.target" ];
    serviceConfig = {
      ExecStart = "${pkgs.pgadmin4}/bin/pgadmin4";
      User = "your-username";  # ⚠️ Replace with your actual system username
      Restart = "always";
    };
  };
}
```

Then apply the changes:

```bash
sudo nixos-rebuild switch
```

---

## 🛠 2. Initialize PostgreSQL

If not automatically initialized, run:

```bash
sudo -u postgres initdb /var/lib/postgresql
```

Start the PostgreSQL shell to set up your user:

```bash
sudo -u postgres psql
```

In the shell, create a user and database:

```sql
CREATE USER myuser WITH PASSWORD 'mypassword';
CREATE DATABASE mydb OWNER myuser;
\q
```

---

## 🚀 3. Run and Access pgAdmin4

Start the service:

```bash
Sudo pgadmin4
```


Then go to [http://localhost:5050](http://localhost:5050) in your browser.

---

## 🔧 4. Configure pgAdmin4 in Browser

1. Set a **master password** on first login.
2. Click **Add New Server**.
3. Under the **Connection** tab, fill in:

   - **Host name/address**: `localhost`
   - **Port**: `5432`
   - **Maintenance database**: `postgres`
   - **Username**: `myuser`
   - **Password**: `mypassword`

Click **Save** to connect.

---

## ✅ You're Done

You now have:

- A working PostgreSQL server on NixOS
- pgAdmin4 running on `http://localhost:5050`
- A user and database ready to go

---

## 📝 Extra Notes

- Data is stored by default in `/var/lib/postgresql`
- Use `.pgpass` or `PGPASSWORD` for automated login in scripts
- You can use `pg_dump` to export backups and `psql` to restore them

---

## 🔐 Security Tips

- Don’t use the `postgres` user for daily work
- Use strong passwords and minimal privileges
- Keep your browser access restricted to `localhost`

---

## 📚 References

- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [pgAdmin Docs](https://www.pgadmin.org/docs/)
- [NixOS PostgreSQL Options](https://search.nixos.org/options?channel=stable&show=services.postgresql)
