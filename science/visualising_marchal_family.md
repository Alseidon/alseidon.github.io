+++
title = "Visualising Marchal's family"
hasmath = true
hascode = true
date = Date("22/01/2026", "dd/mm/yyyy")

tags = ["research"]
+++

# Visualising Marchal's family

In a few [talks](/talks), I presented a visualisation of what I call the _Marchal family_ of quasi-periodic orbits. This visualisation was done in [GLMakie](https://makie.org), a great [Julia](https://www.julialang.org) package for advanced interactive visualisation.

Below, I show the code for the visualisation, which is 276 lines long; the `#region`, `#endregion` and `##` comments are for ease of use within VS Code with the Julia extension. The imported data is a file of about 250 Mo; I have no convenient way of sharing it right now; however, if you contact me I will gladly share it!

The `Project.toml` is really short:
```toml
[deps]
GLMakie = "e9467ef8-e4e7-5192-8a1a-b1aee30e663a"
```
And here is the code:

```julia
#region Setup
dir = @__DIR__
using Pkg; Pkg.activate(dir)
using GLMakie
using JLD
using StaticArrays

Makie.inline!(false)
set_theme!(Theme(fontsize = 24))


function rot1(θ)
    return @SMatrix(
    [1      0      0;
     0 cos(θ) -sin(θ);
     0 sin(θ)  cos(θ)])
end

function rot3(θ)
    return @SMatrix(
    [cos(θ)  -sin(θ)  0;
     sin(θ)   cos(θ)  0;
          0        0  1])
end
acos_clamped(val) = acos(clamp(val, -1, 1))

masses(ε_m) = (1-2ε_m, ε_m, ε_m)

vec_arr3_to_arr4(vec) = [vec[i][j, k, l] for
    i in eachindex(vec), j in axes(vec[1], 1),
    k in axes(vec[1], 2), l in axes(vec[1], 3)
]

arr3_vec_to_arr4(arr) = [arr[i, j, k][l] for
    i in axes(arr, 1), j in axes(arr, 2),
    k in axes(arr, 3), l in eachindex(arr[1, 1, 1])
]


function hill2cart_helio_norot(x, masses)
    rt1, rt2, G1, G2, r1, r2, w1, w2, C, Ω1 = x
    x_cart = zeros(eltype(x), 18)
    J = acos_clamped((C^2 - G1^2 - G2^2) / (2 * G1 * G2))
    # rt1 *= (masses[1] + masses[2]) / masses[2]
    # rt2 *= (masses[1] + masses[3]) / masses[3]
    # G1  *= (masses[1] + masses[2]) / masses[2]
    # G2  *= (masses[1] + masses[3]) / masses[3]
    i1 = acos_clamped((G1 + G2 * cos(J)) / C)
    i2 = acos_clamped((G2 + G1 * cos(J)) / C)
    #i1 = J / 2
    #i2 = J / 2
    Ω2 = Ω1 + π
    R1 = rot3(Ω1) * rot1(i1) * [r1 * cos(w1), r1 * sin(w1), 0]
    R2 = rot3(Ω2) * rot1(i2) * [r2 * cos(w2), r2 * sin(w2), 0]
    Rt1 = rot3(Ω1) * rot1(i1) * rot3(w1) * [rt1, G1/r1, 0]
    Rt2 = rot3(Ω2) * rot1(i2) * rot3(w2) * [rt2, G2/r2, 0]
    #= Rt1 = rot3(Ω1) * rot1(i1) * [
              rt1 * cos(w1) - G1 * sin(w1) / r1,
              rt1 * sin(w1) + G1 * cos(w1) / r1,
              0
          ]
    Rt2 = rot3(Ω2) * rot1(i2) * [
              rt2 * cos(w2) - G2 * sin(w2) / r2,
              rt2 * sin(w2) + G2 * cos(w2) / r2,
              0
          ] =#
    x_cart[4:6] = R1
    x_cart[7:9] = R2
    x_cart[13:15] = Rt1# ./ masses[2]
    x_cart[16:18] = Rt2# ./ masses[3]
    return x_cart
end

function hill2cart_helio_draconitic(x, masses)
    return hill2cart_helio_norot(vcat(x[1:9], 0.), masses)
end

function hill2cart_bary(x, masses)
    x_cart = hill2cart_helio_norot(x, masses)
    m0, m1, m2 = masses
    x_bary = zero(x_cart)
    x_bary[4:6] = x_cart[4:6] - (m2 / (m0 + m2)) * x_cart[7:9]
    x_bary[4:6] ./= (1 + m1/(m0+m2))
    x_bary[7:9] = x_cart[7:9] - (m1 / (m0 + m1)) * x_cart[4:6]
    x_bary[7:9] ./= (1 + m2/(m0+m1))
    x_bary[1:3] = -(m1 * x_bary[4:6] + m2 * x_bary[7:9]) / m0
    x_bary[13:15] = x_cart[13:15]
    x_bary[16:18] = x_cart[16:18]
    x_bary[10:12] = -(x_bary[13:15] + x_bary[16:18])
    return x_bary
end

function rot3_point(vec, angle)
    return reduce(vcat,
        [rot3(angle) * vec[3i .+ (1:3)] for i in 0:5]
    )
end

function rot3_trajs_arr4(trajs, angles)
    return [
        rot3_point(trajs[i, j, k, :], angles[i, j, k])
        for i in axes(trajs, 1), j in axes(trajs, 2), k in axes(trajs, 3)
    ] |> arr3_vec_to_arr4
end

#endregion
##

#region Load trajectories
data_trajs_file = joinpath(dir, "data", "data_trajs_eps_01_33_J_000_160.jld")

data_trajs = load(data_trajs_file)
vec_ts = data_trajs["vec_ts"]
vec_ε_m = data_trajs["vec_ε_m"]
vec_Js = data_trajs["vec_Js"]
arr_eigvals = data_trajs["arr_eigvals"]
arr_trajs = data_trajs["arr_trajs"]

##

vec_masses = [masses(eps_m) for eps_m in vec_ε_m]

trajs_helio_draco = [
    hill2cart_helio_draconitic(arr_trajs[ieps, iJ, it, :], vec_masses[ieps])
    for ieps in eachindex(vec_ε_m), iJ in eachindex(vec_Js), it in eachindex(vec_ts)
] |> arr3_vec_to_arr4
trajs_helio_norot = [
    hill2cart_helio_norot(arr_trajs[ieps, iJ, it, :], vec_masses[ieps])
    for ieps in eachindex(vec_ε_m), iJ in eachindex(vec_Js), it in eachindex(vec_ts)
] |> arr3_vec_to_arr4
trajs_bary = [
    hill2cart_bary(arr_trajs[ieps, iJ, it, :], vec_masses[ieps])
    for ieps in eachindex(vec_ε_m), iJ in eachindex(vec_Js), it in eachindex(vec_ts)
] |> arr3_vec_to_arr4

t_lag = 1.
angles_antirot = [
    (
        2π - arr_trajs[ieps, iJ, end, 10]
    ) / t_lag * vec_ts[it] for ieps in eachindex(vec_ε_m), iJ in eachindex(vec_Js), it in eachindex(vec_ts)
]
trajs_bary_antirot = rot3_trajs_arr4(trajs_bary, angles_antirot);

##
arr_to_traj_points(arr_traj) = map(
    i_body->[
        Point3f(arr_traj[i, j, k, 3i_body .+ (1:3)]...)
        for i in axes(arr_traj, 1), j in axes(arr_traj, 2),
            k in axes(arr_traj, 3)
    ],
    0:2
)

traj_points_helio_draco = arr_to_traj_points(trajs_helio_draco)
traj_points_helio_norot = arr_to_traj_points(trajs_helio_norot)
traj_points_bary = arr_to_traj_points(trajs_bary)
traj_points_bary_antirot = arr_to_traj_points(trajs_bary_antirot)

traj_points = [
    traj_points_helio_draco,
    traj_points_helio_norot,
    traj_points_bary,
    traj_points_bary_antirot,
];
#endregion
##

#region 3D figure
fig_3D = Figure()#; backgroundcolor=:lightgrey)
ax_3D = Axis3(fig_3D[1, 1])
slider_3D = Slider(fig_3D[1, 2];
    range=eachindex(vec_Js),
    horizontal=false,
    linewidth=20,
    startvalue=1
)
i_J = lift(identity, slider_3D.value)
text_J = lift(i_J) do iJ
    return "J = $(
        round(rad2deg(vec_Js[iJ]); sigdigits=3))°"
end
label_3D = Label(fig_3D[2, 2],
    text_J
)
play_button = Button(fig_3D[2, 1], label="Play", tellwidth=false)
is_playing = false
end_n = length(vec_ts)
function play!(_)
    if !is_playing
        @async for i in eachindex(vec_ts)
            if i == 1
                global is_playing = true
            elseif i == end_n
                global is_playing = false
            end
            i_t[] = i
            sleep(0.05)
        end
    end
end 
on(play!, play_button.clicks)
on(events(ax_3D.scene).keyboardbutton) do event
    if event.action == Keyboard.press || event.action == Keyboard.repeat
        if event.key == Keyboard.space
            play!(nothing)
        end
    end
end

slider_ε = Slider(
    fig_3D[3, 1];
    range=eachindex(vec_ε_m),
    horizontal=true,
    linewidth=20,
    startvalue=1
)
i_ε = lift(identity, slider_ε.value)
text_ε = lift(i_ε) do ieps
    return "ε_m = $(
        round(vec_ε_m[ieps]; sigdigits=3))"
end
label_ε = Label(fig_3D[3, 2], text_ε, tellwidth=false, width=160)


slider_typetraj = Slider(
    fig_3D[4, 1];
    range=1:4,
    horizontal=true,
    linewidth=20,
    startvalue=1
)
i_typetraj = lift(identity, slider_typetraj.value)
text_typetraj = lift(i_typetraj) do itraj
    return ["Helio rotating", "Helio", "Bary", "Bary antirot"][itraj]
end
label_typetraj = Label(fig_3D[4, 2], text_typetraj)

get_points(new_iJ, i_ε, i_traj, i_body) = traj_points[i_traj][i_body][
    i_ε, new_iJ, :]
plotted_mass0 = lift(
    (iJ, ieps, i_traj)->get_points(iJ, ieps[], i_traj[], 1),
    i_J, i_ε, i_typetraj)
plotted_mass1 = lift(
    (iJ, ieps, i_traj)->get_points(iJ, ieps[], i_traj[], 2),
    i_J, i_ε, i_typetraj)
plotted_mass2 = lift(
    (iJ, ieps, i_traj)->get_points(iJ, ieps[], i_traj[], 3),
    i_J, i_ε, i_typetraj)

limits!(ax_3D, (-1.5, 1.5), (-1.5, 1.5), (-1.5, 1.5))
lines!(ax_3D, plotted_mass0; linewidth=1)
lines!(ax_3D, plotted_mass1; linewidth=1)
lines!(ax_3D, plotted_mass2; linewidth=1)

i_t = Observable(1)
points = lift(i_t, plotted_mass0, plotted_mass1, plotted_mass2) do it, traj0, traj1, traj2
    return [traj0[i_t[]], traj1[i_t[]], traj2[i_t[]], traj0[i_t[]]]
end
scatter!(ax_3D, points)
lines!(ax_3D, points;
    linestyle=:dash
)


fig_3D

##
for i in eachindex(vec_ts)
    i_t[] = i
    sleep(0.05)
end
##

#endregion
##
```