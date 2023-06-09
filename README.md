# 2D-unsteady-heat-generation
% Define the geometry and physical parameters
L = 1; % length of the plate (m)
W = 1; % width of the plate (m)
q0 = 1000; % heat source intensity (W/m^2)
k = 50; % thermal conductivity of the plate (W/m*K)
h = 100; % heat transfer coefficient at the plate surface (W/m^2*K)
T_inf = 25; % ambient temperature (°C)

% Define the mesh
nx = 50; % number of nodes in x-direction
ny = 50; % number of nodes in y-direction
dx = L/nx; % node spacing in x-direction
dy = W/ny; % node spacing in y-direction
x = linspace(0, L, nx+1); % node locations in x-direction
y = linspace(0, W, ny+1); % node locations in y-direction
[X, Y] = meshgrid(x, y); % grid of node locations

% Define the boundary conditions
T = T_inf*ones(ny+1,nx+1);
T(1,:) = T_inf; % top boundary
T(ny+1,:) = T_inf; % bottom boundary
T(:,1) = T_inf; % left boundary
T(:,nx+1) = T_inf; % right boundary
q = q0*ones(ny+1,nx+1);
q(ny/2-2:ny/2+2, nx/2-2:nx/2+2) = 0; % set the heat source to zero in a small region around the center

% Solve the heat transfer equation
T_old = T;
err = 1e-6;
while true
    for i = 2:nx
        for j = 2:ny
            T(j,i) = (1/4)*(T_old(j,i+1)+T_old(j,i-1)+T_old(j+1,i)+T_old(j-1,i))+((dx*dy*q(j,i))/(4*k));
        end
    end
    T(1,:) = T_inf; % top boundary
    T(ny+1,:) = T_inf; % bottom boundary
    T(:,1) = T_inf; % left boundary
    T(:,nx+1) = T_inf; % right boundary
    if max(abs(T(:)-T_old(:))) < err
        break
    end
    T_old = T;
end

% Plot the temperature distribution
contourf(X,Y,T,'LineColor','none')
colorbar
title('Temperature Distribution')
xlabel('x (m)')
ylabel('y (m)')
